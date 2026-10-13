---
name: oauth-flow
description: Implements OAuth 2.0 and OpenID Connect correctly - authorization code with PKCE, client credentials for machine-to-machine, state and nonce, exact redirect URI matching, safe token storage, refresh rotation with reuse detection, scopes, ID token validation via JWKS, and logout - aligned with RFC 9700 (OAuth Security BCP) and RFC 7636 (PKCE). Use when someone asks "add Google or Microsoft login", "implement OAuth in our app", "where should I store the refresh token", "how do I validate an ID token", or is wiring an app to an identity provider or third-party API. Do NOT use for auditing a whole application's security posture - use security-audit instead; for line-level review of an auth diff, use secure-code-review.
metadata:
  title: "OAuth & OIDC Flow"
---

# OAuth & OIDC Flow

OAuth bugs fail silently: a login that works in the demo can still accept forged callbacks, leak refresh tokens to page scripts, or link an attacker's account to a victim's. The costly mistake this skill prevents is copying a flow that "works" - implicit grant, tokens in localStorage, unchecked state, an ID token decoded but never verified - and shipping an account-takeover path no functional test will catch.

## Operating procedure

Decide the grant and token storage before writing a route; most OAuth flaws are architecture choices.

### Step 1: Choose the grant

- A user signs in (web, SPA, native): authorization code + PKCE. RFC 9700 requires PKCE for public clients and recommends it for confidential ones - use it for both.
- No user present: client credentials, confidential clients only.
- Never implicit (fragment tokens leak via history and scripts) or password credentials (RFC 9700: must not be used).
- SPAs: prefer a backend-for-frontend (BFF) that holds tokens and gives the browser only an httpOnly session cookie.
- Native apps: system browser, not a webview (RFC 8252).

### Step 2: Register the client tightly

- Redirect URIs use exact string matching - no wildcards or prefixes; only a native app's loopback port may vary.
- Separate clients and redirect URIs per environment.
- Prefer private_key_jwt or mTLS client auth over shared secrets; secrets stay server-side.
- Request minimum scopes, add more incrementally, and check the granted scope in the token response.

### Step 3: Build the authorization request

- code_verifier: 43-128 characters from [A-Z a-z 0-9 - . _ ~]; 32 random bytes base64url-encoded gives 43.
- code_challenge = BASE64URL(SHA-256(code_verifier)), unpadded, with code_challenge_method=S256. Never plain.
- state: at least 128 random bits, bound to the browser session, single use. PKCE blocks code injection, but state still stops login CSRF where you cannot verify the provider enforces PKCE.
- nonce (OIDC): random, later compared to the ID token's nonce claim.

Keep verifier, state, nonce, and return path in a server-side transaction or encrypted httpOnly cookie (~10 minute TTL). Allowlist the return path, or the callback becomes an open redirector.

### Step 4: Handle the callback

1. On error show a generic failure. Compare state in constant time; reject on mismatch or missing transaction; delete the transaction (single use).
2. If an iss parameter is returned (RFC 9207), require the expected issuer - this blocks mix-up attacks.
3. Exchange the code server-side with the identical redirect_uri, the code_verifier, and client authentication.
4. Validate the ID token (Step 5) before creating a session; rotate the session ID; redirect to the validated return path.

### Step 5: Validate the ID token

Never just decode it. Verify:

- Signature via the provider's JWKS, key chosen by kid; on an unknown kid refetch once (rate limited) to follow rotation.
- alg in an explicit allowlist (e.g. RS256, ES256); reject none.
- iss exactly equals the discovered issuer; aud contains your client_id (with multiple audiences, azp equals client_id).
- exp in the future, clock skew of 60 seconds or less; nonce equals the stored value.

Key users by (iss, sub), never email; auto-link by email only when email_verified is true and the provider is authoritative for the domain. The ID token is not an API credential - APIs take the access token and check its audience.

### Step 6: Store tokens

- Web apps and BFFs: server-side session store or an encrypted httpOnly, Secure, SameSite cookie.
- SPAs without a backend: access token in memory only; never refresh tokens in localStorage or sessionStorage - any XSS reads them.
- Native: Keychain or Android Keystore.
- Never put tokens in URLs, logs, or error reports.

### Step 7: Refresh with rotation and reuse detection

- Each refresh issues a new refresh token and invalidates the old one; presentation of an already-used token revokes the whole token family.
- Public clients must use rotation or sender-constrained tokens (DPoP or mTLS) per RFC 9700.
- Make refresh single-flight so concurrent tabs do not trip reuse detection; refresh ~60 seconds before exp.

### Step 8: Logout

Destroy the session, revoke the refresh token (RFC 7009), and for single sign-out redirect to end_session_endpoint with id_token_hint. Clearing a cookie alone leaves the refresh token valid.

## Worked example

Next.js App Router BFF using jose. seal/unseal, createSession, fail, and basicAuth are your helpers.

```ts
// imports: NextRequest, NextResponse (next/server); createRemoteJWKSet, jwtVerify (jose);
//          randomBytes, createHash, timingSafeEqual (node:crypto)

// app/api/auth/login/route.ts
export async function GET(req: NextRequest) {
  const verifier = randomBytes(32).toString("base64url");
  const challenge = createHash("sha256").update(verifier).digest("base64url");
  const state = randomBytes(32).toString("base64url");
  const nonce = randomBytes(32).toString("base64url");
  const returnTo = safeReturnPath(req.nextUrl.searchParams.get("returnTo"));

  const url = new URL(OIDC.authorization_endpoint);
  url.search = new URLSearchParams({
    response_type: "code", client_id: CLIENT_ID, redirect_uri: REDIRECT_URI,
    scope: "openid email profile", state, nonce,
    code_challenge: challenge, code_challenge_method: "S256",
  }).toString();
  const res = NextResponse.redirect(url);
  res.cookies.set("oauth_tx", await seal({ verifier, state, nonce, returnTo }), {
    httpOnly: true, secure: true, sameSite: "lax", path: "/api/auth", maxAge: 600,
  });
  return res;
}

// app/api/auth/callback/route.ts
const JWKS = createRemoteJWKSet(new URL(OIDC.jwks_uri));

export async function GET(req: NextRequest) {
  const p = req.nextUrl.searchParams;
  const tx = await unseal(req.cookies.get("oauth_tx")?.value);
  const state = p.get("state") ?? "";
  const code = p.get("code");
  if (!tx || !code || state.length !== tx.state.length ||
      !timingSafeEqual(Buffer.from(state), Buffer.from(tx.state))) return fail();
  if (p.get("iss") && p.get("iss") !== OIDC.issuer) return fail();

  const r = await fetch(OIDC.token_endpoint, {
    method: "POST",
    headers: { "Content-Type": "application/x-www-form-urlencoded",
               Authorization: basicAuth(CLIENT_ID, CLIENT_SECRET) },
    body: new URLSearchParams({ grant_type: "authorization_code", code,
      redirect_uri: REDIRECT_URI, code_verifier: tx.verifier }),
  });
  if (!r.ok) return fail();
  const tokens = await r.json();

  const v = await jwtVerify(tokens.id_token, JWKS, { issuer: OIDC.issuer,
    audience: CLIENT_ID, algorithms: ["RS256"], clockTolerance: 60,
  }).catch(() => null);                      // throws on any failed check
  if (!v || v.payload.nonce !== tx.nonce) return fail();
  const { payload } = v;

  const res = NextResponse.redirect(new URL(tx.returnTo, req.url));
  res.cookies.delete({ name: "oauth_tx", path: "/api/auth" });
  await createSession(res, { userKey: `${payload.iss}|${payload.sub}`,
                             refreshToken: tokens.refresh_token });
  return res;
}
```

With response_mode=form_post (Apple, when requesting name or email) the callback is a cross-site POST that drops SameSite=Lax cookies: use SameSite=None; Secure and read the form body.

## Provider gotchas

- Google: refresh tokens need access_type=offline and usually arrive only on first consent.
- Microsoft Entra ID: on the common or organizations endpoints iss carries the user's tenant ID - validate against an allowlist of tenant issuers.
- GitHub OAuth Apps are not OIDC: no ID token; key on the numeric user ID.
- Apple: name arrives only on first authorization; the client secret is a JWT you sign, valid six months at most.

## Thresholds

- code_verifier 43-128 chars; state and nonce at least 128 bits; transaction TTL ~10 minutes, single use.
- Access tokens 5-60 minutes; rotated refresh tokens with idle timeout and absolute lifetime (e.g. 30-90 days).
- ID token clock skew 60 seconds or less; one refresh-and-retry per 401.

## Deliverable

A working integration plus a design note (grant, client auth, redirect URIs per environment, token locations, rotation, revocation, ID token checks, logout) and negative tests for state mismatch, nonce mismatch, wrong audience, expired token, and refresh token reuse.

## Do NOT

- Do not use implicit or password grants, PKCE plain, or wildcard redirect URIs.
- Do not store refresh tokens in localStorage, sessionStorage, or non-httpOnly cookies.
- Do not accept an ID token without verifying signature, iss, aud, exp, and nonce; never accept alg none.
- Do not use the ID token as an API bearer token, or link accounts by unverified email.

## Quality bar

- Every check (state, nonce, iss, aud, exp, signature, reuse) has a negative test that fails closed.
- No token in a URL, log line, or script-readable storage; key rotation needs no deploy; logout makes the refresh token unusable.

## Escalation

Use security-audit for the surrounding app, secure-code-review for the auth diff, threat-model-stride for account-linking designs, secrets-hygiene for a leaked client secret, and api-design for how APIs accept access tokens.
