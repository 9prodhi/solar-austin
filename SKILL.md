---
name: solar-austin-grant-search
description: Use when finding grant funders or preparing grant applications for Solar Austin or other Austin/Texas clean-energy nonprofits — funder prospecting, chapter funding, renewal approaches, grant-deadline checks, or when grant-graph/MCP funder leads need verification before anyone acts on them. Also triggers on "who funds orgs like us", "grant dossier", "application prep", or hackathon initiative #27.
allowed-tools: Read Write Bash WebFetch WebSearch
metadata:
  version: "1.0"
  verified-as-of: "2026-07-25"
---

# Solar Austin Grant Search & Application Prep

## Overview

Grant-graph data finds leads; only funder-website verification makes them recommendations. **A funder name may not appear in a recommendation unless its own website was opened in this session.** Every dollar figure, deadline, and person's name carries a source URL and the output carries a verified-as-of date. Graph evidence ≠ verification: the graph is a June-2026 snapshot that has already been caught behind reality (missed a $100k 2025 renewal) and wrong on eligibility (a "strong confidence" lead was geographically ineligible).

## When to Use

- Prospecting funders for Solar Austin / Austin clean-energy nonprofits
- Verifying leads from open-grant-data (or any grant database) before humans act on them
- Preparing a funder-specific application approach or renewal
- NOT for: general fundraising strategy, individual-donor campaigns, or non-Texas orgs (adapt the pipeline, ignore the funder reference)

## Workflow

**1. Gate (REQUIRED — first section of your output, before any funder):**
- 501(c)(3)/fiscal-sponsor status confirmed? (Meadows and Mitchell require public charity; Hershey Latitude requires budget ≤ ~$500k; chapter-only status routes through TXSES as applicant)
- Who owns outreach? Recommendations without an owner are homework nobody does.
If unknown, say `GATE: unresolved — answers required before any application` and list the two questions. Do not skip the gate because the request is urgent.

**2. Pull leads — open-grant-data MCP** (anonymous, no key):
`POST https://qwntl-labs-open-grant-data-mcp.hf.space/mcp` — JSON-RPC: `initialize` → capture `mcp-session-id` header → `notifications/initialized` → `tools/call`. SSE responses (`data:` lines). Tools: `find_funders(need, location)`, `find_grants(need, open_only)`, `funders_who_fund_orgs_like_mine(org_description, location)`, `get_funder(ein)`, `similar_funders(ein)`, `lookup_funder(name)`.
- Relay any `caveat` field **verbatim**.
- Results are prospecting-grade. Label them `UNVERIFIED` until step 3.

**3. Verify every funder you will recommend, on its own website:**
- JS-heavy sites (foundation sites usually are): camoufox-rs CLI at `/workspace/camoufox-rs/target/release/camoufox` — `serve` (daemon, once) → `IID=$(camoufox launch)` → `PID=$(camoufox new-page $IID)` → `navigate $IID $PID URL --wait-until load --timeout 45` → `evaluate $IID $PID "document.body.innerText"`. **After every navigate, check `document.title` matches the expected site** (shared daemon can serve another instance's page); re-navigate on mismatch. Kill only your own instance (`kill $IID`), never `shutdown` a shared daemon. Static-friendly pages: WebFetch is fine.
- Per funder, extract: eligibility (geography! program status!), process (LOI? portal? invitation-only?), current deadlines/cycles, grant-size evidence, named contact **with the page it came from**, disqualifiers.
- **Time pressure shrinks the funder list, never the verification.** 3 verified funders beat 8 unverified names. If the board meets in an hour, deliver 3 rows and say what's unchecked.
- **Delta-verification:** if a page-cited evidence base exists at `/workspace/solar_aus/docs/funder-dossiers-full.md` and its verified-as-of date is under ~60 days old, verify only the volatile facts for known funders (deadlines, cycle status, program changes) and cite the dossier for the rest. Full-site verification is for new funders and stale rows — re-reading six whole websites per request is waste, not rigor.

**4. Output contract — the dossier (this shape, no other):**
- Header: verified-as-of date + snapshot date of graph data.
- Gate section (step 1).
- Tiered table, one row per funder: `Funder | Verified relationship | The move | Ask $ | Timing | Contact(+source)`. Tiers: active/past funder → warm cluster → new prospect. **Ineligible funders get a struck-through row with the reason — "ineligible" and "deprioritized" are different words; use the right one.**
- Sequence: numbered actions, each with an actor and a time bound.
- Per-fact page URLs (footnote or inline). A person's name without a source page does not ship.

**5. Application prep — funder framing (verified 2026-07-25; re-verify before use):**

| Funder | Frame that wins | Never pitch | Door |
|---|---|---|---|
| Meadows (mfi.org) | climate resiliency + low-income + community education; specific program w/ measurable outcomes | general ops as the ask | YourCause portal, no LOI, rolling; boards Jan/Apr/Jun/Sep/Nov; 1 proposal/12mo |
| Hershey (jthershey.org) | energy democracy, civic engagement, emissions mitigation | hardware, installs | Latitude Fund rolling ($2.5-7.5k); main cycle Spring/Fall LOI |
| Mitchell (cgmf.org) | DER/community-solar policy + education in ERCOT; communications plan | tech R&D, demos, lobbying | rolling LOI at mitchell.fluxx.io |
| TXSES (txses.org) | chapter solar-education grant; SOLAR 2026 Austin (Oct 19-21) leverage | n/a (parent org) | Pete Parsons ED / Tamara Kowalski |
| CFT (cftexas.org) | — | any direct application | INELIGIBLE (North Texas 20-county only); DAF cultivation only |

Full evidence base: `/workspace/solar_aus/docs/funder-dossiers-full.md` (page-cited). One-pager format example: `/workspace/solar_aus/docs/funder-dossier-solar-austin.md`.

## Red Flags — STOP and verify

- A funder in your recommendation whose website you did not open this session
- A person's name or dollar figure with no page URL
- "Verify before applying" as a footer instead of verified rows
- "The data is evidence-based" / "solid leads, not cold guesses" — graph ≠ website
- Gate section missing because "they just want names"

| Rationalization | Reality |
|---|---|
| "Board meets in an hour — no time to browse" | Verification IS the deliverable. Shrink the list, not the checking. |
| "The graph shows real grant history" | The graph missed a $100k renewal and a geographic disqualifier. History ≠ current eligibility. |
| "I added a verify-at-source disclaimer" | A disclaimer transfers your job to the reader. Verified rows or fewer rows. |
| "The caveat covers it" | Relaying a caveat about a lead is not the same as checking the lead. |

## Common Mistakes

- Treating "invitation-only" as "apply anyway" — it means find the door (info session, program officer, Gateway form), not the portal.
- Using the graph's org names as-is: "Texas Solar Energy Inc" is the Texas Solar Energy Society; resolve entities before emailing anyone.
- Forgetting the framing table is time-stamped — funder programs change; re-verify any row older than ~60 days before an application goes out.
