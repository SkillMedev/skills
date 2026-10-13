---
name: mcp-builder
description: Design and build Model Context Protocol servers that models use correctly - choosing stdio vs stateless Streamable HTTP while still serving 2025-era clients, shaping a few task-level tools with clear descriptions, JSON Schema inputs and honest annotations, bounding and paginating output, returning recoverable errors as isError results, securing remote servers with OAuth 2.1, and testing with the MCP Inspector. Use when someone asks "build an MCP server for our API", "turn this service into MCP tools", "why does the model keep calling the wrong tool", "add OAuth to my remote MCP server", or "my stdio server keeps disconnecting". Do NOT use for designing a REST or GraphQL API consumed by human-written code - use api-design instead - or for coordinating multiple LLM agents - use agent-orchestration instead.
metadata:
  title: "MCP Server Builder"
---

# MCP Server Builder

An MCP server is an API whose caller is a language model. It picks tools by name and description, pays in context for every byte returned, and cannot read your docs. The costly mistake this skill prevents is wrapping a REST API one endpoint per tool. That produces forty thin tools, vague descriptions, unbounded JSON and errors thrown as protocol failures, and the result is a server the model misuses, that floods the context window, and that fails silently.

## Operating procedure

### Step 1: Write the task list before the tool list

List the 5-10 concrete things users will ask for, such as "find open bugs assigned to me". Each task should take one or two calls. One `search_issues` with filters beats three list-and-filter tools; chain upstream calls inside the server.

### Step 2: Choose the transport and protocol posture

- **stdio** is for a local subprocess (filesystem, git, CLIs). On stdio, stdout belongs to the protocol: log to stderr, never `console.log` or `print`.
- **Streamable HTTP** is for remote or shared servers. Since spec revision 2026-07-28 the protocol is stateless:
  - There are no sessions, no `Mcp-Session-Id` and no `initialize` handshake. Each request carries its protocol version and client capabilities in `_meta`.
  - Servers MUST implement `server/discover` to advertise their versions, capabilities and identity. Change notifications go over `subscriptions/listen`.
  - Put cross-call state in server-minted handles that are passed back as ordinary tool arguments, not in connection state.
- Many deployed clients still speak 2025-era revisions, which use `initialize` and sessions, so serve both eras. Use an SDK entry point that negotiates the era rather than hand-rolling the wire format.
- The old HTTP+SSE transport is deprecated, so do not build on it.

### Step 3: Pick the right primitive

- **Tools** are actions or queries the model invokes on its own.
- **Resources** are addressable, read-only context that the app or user attaches.
- **Prompts** are templates the user picks, often shown as slash commands.

Do not adopt the deprecated Roots, Sampling or Logging features in new servers. Instead, take paths as tool parameters, call your LLM provider directly, and log to stderr or OpenTelemetry. If a call needs more input from the user mid-flight, use the multi round-trip pattern. The server returns an input-required result (`resultType: "input_required"`) and the client retries the request with the answers. The server never sends its own requests to the client.

### Step 4: Design each tool's contract

- **Name** it `verb_noun` in snake_case, unambiguous across servers (`search_issues`, not `search`).
- **Description**: the first sentence says what the tool returns and when to use it. Then constraints, next calls, and when not to use it.
- **Input schema**: JSON Schema with types, enums for closed sets, defaults, and a description on every property. Accept human identifiers like `PROJ-123`, not internal IDs the model cannot know.
- **Annotations**: set `readOnlyHint`, `destructiveHint`, `idempotentHint` and `openWorldHint` honestly, plus a `title`. They drive the client's confirmation UX but are not a security boundary.
- **Output**: concise text the model can reason over. Add `outputSchema` and `structuredContent` when machine consumers need them.
- **Order**: return the tool list in a deterministic order so client and prompt caches hit.

### Step 5: Bound every response

- Cap lists at a default of 20 and a hard max of 100, and return an opaque cursor for the next page.
- Truncate long fields and say so, for example: "truncated; call get_issue for the full text".
- Budget in UTF-8 bytes, not items or JS string length, because one item can be 500 KB. Cut on a character boundary.

### Step 6: Handle errors in the right layer

- **Tool execution errors** return a result with `isError: true` and an actionable message: what failed and what to try next. This covers an upstream 404, a rate limit, or bad arguments the model can fix. The TypeScript SDK v2 already returns schema-validation failures this way.
- **Protocol errors** are JSON-RPC errors such as unknown method, malformed request or `UnsupportedProtocolVersionError`. The model often never sees them.

### Step 7: Secure remote servers

- Act as an OAuth 2.1 resource server. Unauthenticated requests get `401` with `WWW-Authenticate: Bearer resource_metadata="https://host/.well-known/oauth-protected-resource"`.
- Serve protected resource metadata (RFC 9728) that names your authorization server. PKCE is mandatory. Clients should register with Client ID Metadata Documents; Dynamic Client Registration is deprecated and kept only for compatibility.
- Check that every token's audience is your server. Never pass the client's token through to upstream APIs.
- Validate the `Origin` header on every Streamable HTTP server. Bind local servers to `127.0.0.1`.

### Step 8: Test, then version

- Run the MCP Inspector (`npx @modelcontextprotocol/inspector <server command>`) on every tool: valid, invalid, empty, last page, upstream outage. Test a current and a 2025-era client.
- Try real prompts: if the model picks the wrong tool, fix the description.
- Tool names and schemas are a public API: add optional fields freely, never repurpose a name or change semantics.

## Worked example - TypeScript SDK v2, stdio

```typescript
import { McpServer } from "@modelcontextprotocol/server";
import { serveStdio } from "@modelcontextprotocol/server/stdio";
import * as z from "zod/v4";

const MAX_BYTES = 24_000;

// Cut at a code-point boundary once the UTF-8 byte budget is spent.
function clip(text: string): string {
  let bytes = 0, end = 0;
  for (const ch of text) {
    bytes += Buffer.byteLength(ch);
    if (bytes > MAX_BYTES) return text.slice(0, end) + "\n[truncated - narrow the query]";
    end += ch.length;
  }
  return text;
}

function buildServer() {
  const server = new McpServer({ name: "issues", version: "1.2.0" });
  server.registerTool(
    "search_issues",
    {
      title: "Search issues",
      description:
        "Returns up to `limit` issues (key, title, status, assignee) matching a query; use it to find issues before reading or updating one. Paged via `cursor`; use get_issue for full text.",
      inputSchema: z.object({
        query: z.string().min(1).describe("Free-text search over title and body"),
        status: z.enum(["open", "closed", "any"]).default("open"),
        limit: z.number().int().min(1).max(100).default(20),
        cursor: z.string().optional().describe("Opaque cursor from a previous call"),
      }),
      annotations: { readOnlyHint: true, openWorldHint: true },
    },
    async ({ query, status, limit, cursor }) => {
      try {
        const page = await tracker.search({ query, status, limit, cursor }); // your API client
        let text = page.items
          .map((i) => `${i.key} [${i.status}] ${i.title} (${i.assignee ?? "unassigned"})`)
          .join("\n") || "No matches. Try a broader query or status=any.";
        text = clip(text);
        if (page.nextCursor) text += `\nMore: cursor=${page.nextCursor}`;
        return { content: [{ type: "text", text }] };
      } catch (err) {
        return { isError: true, content: [{ type: "text", text: `Search failed: ${safeMessage(err)}. Retry shortly if rate-limited.` }] };
      }
    }
  );
  return server;
}

serveStdio(buildServer); // serves current and 2025-era clients by default
```

For HTTP, pass the same factory to `createMcpHandler`. The maintenance-only v1.x SDK (`@modelcontextprotocol/sdk`) speaks only the 2025 era. For Python (FastMCP), check which revisions your version supports.

## Thresholds

- Aim for 5-15 tools per server. Above about 20, tool-selection accuracy tends to drop.
- Description first sentence: under ~200 characters, self-sufficient.
- Page size: default 20, max 100. Budget about 25 KB of text per response.
- Return within ~30 s; longer work needs progress notifications or start/poll tools.
- Every tool has annotations, and every destructive tool sets `destructiveHint: true`.

## Deliverable

A task-to-tool map, transport and era posture with reasons, tool definitions, bounded paginated outputs, an error contract, remote auth setup, Inspector results for both eras, and a README of tools and env vars.

## Do NOT

- Do not mirror REST endpoints one to one.
- Do not keep per-connection state on the current protocol revision.
- Do not throw for recoverable failures or return unbounded payloads.
- Do not treat annotations as authorization or pass client tokens upstream.
- Do not drop 2025-era clients without a deliberate decision.
- Do not guess SDK method names; check the installed version's types.

## Quality bar

- An unprompted model picks the right tool for every task on the list.
- Responses fit the byte budget, truncation is announced, and every failure message is actionable.
- Both protocol eras pass the Inspector checklist.
- The remote server returns a spec-correct 401 challenge and rejects tokens for the wrong audience or Origin.
