---
name: Supply Chain Intelligence
description: Maps a product's supply chain across tiers, scores concentration risk — supplier, geographic, sub-tier, and logistics — and surfaces qualified alternatives with switching cost and qualification lead time for each critical node. Use when someone asks "where are our single points of failure", "map our supplier dependencies", "what happens if this supplier or region goes down", or is building a resilience, dual-sourcing, or nearshoring plan. Do NOT use for mapping competitors rather than suppliers — use competitive-intelligence instead; for quantifying the financial impact of a disruption scenario, pair with cash-flow-forecast.
---

# Supply Chain Intelligence

Build a clear picture of where a product's inputs come from, where the fragile points are, and what alternatives exist. The costly failure this skill prevents is sub-tier blindness: two "diversified" Tier-1 suppliers who both buy the same component from one factory, discovered only when that factory floods. The goal is resilience, not just cost.

## Inputs to collect

1. **The product or product family** being mapped, and its bill of materials if available.
2. **Known Tier-1 suppliers**: name, location, what they supply, contract terms if shareable.
3. **Buffer inventory**: days of cover per critical input (default assumption if unknown: 2-4 weeks for standard components, less for custom — label it a guess).
4. **Existing second sources** and their qualification status.
5. **Constraints**: regulatory requirements (certifications, country-of-origin rules), qualification budget, and how much disruption downtime costs per day — even a rough figure changes the prioritization.

## Operating procedure

### Step 1: Map the supply chain by tier

- **Tier 1**: direct suppliers.
- **Tier 2+**: their suppliers, and so on toward raw materials.

Most catastrophic disruptions originate below Tier 1, so push visibility as deep as sources allow: supplier disclosures, import/customs records, industry reports, certification databases. For each node capture: what it supplies, location, capacity share, and substitutability. Where visibility ends, draw the boundary explicitly on the map — an unmapped tier is a risk finding in itself.

### Step 2: Identify concentration risks

Flag every dependency that creates a single point of failure:

- **Supplier concentration**: one vendor for a critical input.
- **Geographic concentration**: many suppliers clustered in one region, sharing exposure to disaster, conflict, or regulation. Test at three radii: same industrial cluster, same country, same trade bloc.
- **Sub-tier concentration**: multiple Tier-1 suppliers quietly relying on the same Tier-2 source — the hidden choke point, and the one this analysis exists to find.
- **Logistics concentration**: dependence on one port, route, canal, or carrier.

### Step 3: Score each risk

Rate every flagged node on impact and likelihood, 1-5 each:

- **Impact**: how completely does losing this node halt production, and for how long? Anchor on days of buffer inventory: under 2 weeks of cover on a production-halting input scores impact 5; over 12 weeks scores 2 or below.
- **Likelihood**: combine geopolitical exposure, supplier financial health (solvency signals: payment-term stretching, credit downgrades, key-customer loss), natural-hazard exposure, and single-source status. Single-sourced with no qualified alternative scores at least 4.

Priority = impact × likelihood. Everything scoring 15+ is a critical node and proceeds to Step 4; 8-14 goes on the watch list; below 8 is documented and left alone.

### Step 4: Surface alternatives for critical nodes

For each critical node, identify:

- Qualified or qualifiable second-source suppliers in **different regions** (a second source in the same industrial cluster does not reduce geographic risk).
- Substitute materials or design changes that remove the dependence entirely — often cheaper than dual-sourcing.
- Buffer-stock and nearshoring options as bridges.

For every alternative, record the **switching cost** and the **qualification lead time** — an alternative that takes 12 months to qualify is not a short-term mitigation, and presenting it as one creates false comfort. Typical qualification realities: commodity components 1-3 months; custom mechanical parts 3-6 months; regulated or safety-critical components 6-18 months.

### Step 5: Assemble the register and recommend

Rank the register by priority score, summarize the top 3 vulnerabilities, and recommend one concrete move per critical node with an owner and a review date. Supplier data ages fast — date the map and set a refresh cadence (quarterly for critical nodes, annually for the rest).

## Worked artifact: concentration-risk register

Example rows for a consumer hardware product:

| Node | Tier | Risk type | Impact (1-5) | Likelihood (1-5) | Priority | Buffer | Mitigation + lead time |
|---|---|---|---|---|---|---|---|
| Custom battery cell, single plant | 1 | Supplier + geographic | 5 | 4 | 20 | 3 wks | Qualify second cell maker in different country — 6 mo; interim: raise buffer to 8 wks |
| Display driver IC — both display vendors buy from same fab | 2 | Sub-tier | 5 | 3 | 15 | 5 wks | Redesign to support pin-compatible alternate IC — 4 mo |
| All outbound freight via one port | — | Logistics | 3 | 3 | 9 | n/a | Watch list: pre-negotiate alternate-port routing |
| Aluminum enclosure, 3 suppliers, 2 countries | 1 | None material | 2 | 2 | 4 | 6 wks | Document only |

Reading: the sub-tier row is the finding a Tier-1-only analysis would have missed — the two display vendors looked like diversification and were not.

## Deliverable

Produce a supply-chain intelligence pack containing: the tiered supply map with the visibility boundary marked, the ranked concentration-risk register (node, tier, risk type, impact, likelihood, priority, buffer), an alternatives list per critical node with switching cost and qualification lead time, the top-3 vulnerability summary with recommended moves and owners, and the data date with a refresh cadence.

## Do NOT

- Do not stop at Tier 1 — sub-tier concentration is the biggest and least visible risk; where you cannot see, say so rather than implying coverage.
- Do not count a second source in the same region as geographic diversification.
- Do not present an unqualified alternative as a mitigation without its qualification lead time attached.
- Do not optimize cost or resilience blindly — they trade off; state the trade-off and let the owner choose.
- Do not let the map silently age; undated supplier data is worse than none because it looks current.
- Do not use non-public or improperly obtained information; work from public, ethically sourced data and do not infer confidential supplier terms.

## Quality bar

- Every critical input traces to at least Tier 2 or has an explicit visibility-boundary note.
- Every register row has both scores, the buffer figure, and a dated source.
- Every critical node has at least one alternative with switching cost and lead time, or an explicit "no viable alternative — accept and buffer" finding.
- The top-3 summary is actionable: each vulnerability has one named move, an owner, and a review date.
