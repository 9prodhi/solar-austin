# Solar Austin Grant Search

**A Claude Code agent skill that turns grant-database leads into verified, source-linked funder dossiers** for [Solar Austin](https://solaraustin.org/) and other Austin/Texas clean-energy nonprofits.

> Grant databases produce leads. A lead becomes a recommendation only after the
> funder's own website has been checked in the current research session.

## Why this exists

Grant intelligence rots quietly. In the research run that produced this skill, the grant graph:

- **missed a renewal** — it knew about a $75,000 (2023) grant to Solar Austin but not the **$100,000 (2025)** follow-on sitting in the funder's own public grants database;
- **misjudged eligibility** — its top "strong confidence" lead turned out to be **geographically ineligible** (a North-Texas-only community foundation whose Austin grants were donor-advised pass-throughs);
- **missed the clock** — one funder's application window closed *the day before* the research ran, while its rolling small-grants fund was open and nobody would have known.

An unverified lead list transfers the checking work to a volunteer board. This skill does the checking first.

## What it does

```
open-grant-data MCP          funder websites              output
(756k funders, 7.5M-edge  →  (headless browser or     →   tiered dossier:
 who-funds-whom graph)        web fetch, per funder)       verified relationship,
        leads                     verification             move, ask $, timing,
     [UNVERIFIED]              per-fact source URLs        contact + source
                                                           + eligibility GATE
                                                           + action sequence
```

1. **Gate first** — nonprofit/fiscal-sponsor status and a named outreach owner, before any funder is recommended. Unresolved gate = stated blocker, not a skipped step.
2. **Prospect** via the [open-grant-data MCP server](https://huggingface.co/spaces/qwntl-labs/open-grant-data-mcp) (public domain, no API key). All results labeled `UNVERIFIED`; service caveats relayed verbatim.
3. **Verify every recommended funder on its own website** — eligibility (geography!), process, live deadlines, grant-size evidence, named contacts. JS-heavy foundation sites via a [Camoufox](https://github.com/daijro/camoufox) headless browser; static pages via ordinary web fetch.
4. **Deliver the dossier** — tiered table (active/past funder → warm network → new prospect), struck-through ineligible rows with reasons, numbered actions each with an actor and a time bound, `verified-as-of` date, page-level source for every dollar figure, deadline, and person's name.

Under time pressure the skill shrinks the funder list, never the verification: three verified rows beat eight unverified names.

## Example output (abridged, from a real run)

| Tier | Funder | Verified relationship | The move | Ask | Timing |
|---|---|---|---|---|---|
| 1 | Meadows Foundation | ACTIVE: $75k (2023) → **$100k (2025)**, confirmed in mfi.org's own grants DB | Renewal via program officer; new portal account; climate-resiliency + low-income framing | $100–150k | Submit ~4-5 mo before a board month |
| 1 | Hershey Foundation | PAST GRANTEE ($7.5k Latitude, 2024) | Main LOI cycle closed — rolling Latitude Fund is the open door | $2.5–7.5k now | This week |
| — | ~~Communities Fdn of Texas~~ | **INELIGIBLE** — 20-county North Texas rule excludes Travis County (their eligibility page) | DAF-holder cultivation only | n/a | n/a |

## Install

```bash
# personal (all projects)
git clone https://github.com/9prodhi/solar-austin.git ~/.claude/skills/solar-austin-grant-search

# or project-scoped
git clone https://github.com/9prodhi/solar-austin.git .claude/skills/solar-austin-grant-search
```

Review `SKILL.md` before installing — declared tools include file, shell, and web access. Claude Code auto-selects the skill from its description, or invoke it directly: `/solar-austin-grant-search`.

**Optional: register the grant graph as a project MCP server** (`.mcp.json`):

```json
{
  "mcpServers": {
    "open-grant-data": {
      "type": "http",
      "url": "https://qwntl-labs-open-grant-data-mcp.hf.space/mcp"
    }
  }
}
```

Without it, the skill can still drive the endpoint over raw JSON-RPC (documented in `SKILL.md`).

## Example requests

```text
Who funds Austin nonprofits working on community solar and energy democracy?
Create a verified funder dossier for Solar Austin. We have a fiscal sponsor, and Maya owns outreach.
Check whether the deadlines in our grant plan are still current.
Prepare a renewal approach for a past funder: ask, timing, contact, next three actions.
```

## How it was built (and tested)

The skill was developed with test-driven documentation: a baseline agent given the same task **without** the skill claimed "solid, evidence-based leads, not cold guesses" while opening zero funder websites, kept the ineligible funder as a top recommendation, and invented a board chair's name. The same scenario **with** the skill produced live verification of six funder sites, a struck-through ineligible row with the disqualifying source, sourced contacts, an explicit gate, and an honest "what's unchecked" list — under identical time pressure. The rationalizations observed in the baseline run are countered explicitly in `SKILL.md`'s red-flags table.

## Portability and freshness

- The **verification workflow is portable** to any region or sector. The **funder knowledge is not**: the framing table in `SKILL.md` covers five specific Texas funders, verified **2026-07-25** against a **June 2026** graph snapshot. Re-verify any row older than ~60 days before outreach.
- Two workspace-specific paths in `SKILL.md` (the local Camoufox CLI and a page-cited evidence base) should be updated or removed for your environment; absent an evidence base, the skill performs full official-site verification.

## Contributing

Preserve the non-negotiables: official-site verification for every recommendation; page-level sourcing for people, money, and dates; the eligibility/ownership gate; leads honestly separated from verified facts; a visible `verified-as-of` date (update it whenever time-sensitive guidance changes).

## Credits & disclaimer

Built at the Solar Austin **Energy Hackathon** (Austin, July 2026). Grant data: [open-grant-data](https://github.com/qwntl-oss/open-grant-data) (CC0). Browser: [Camoufox](https://github.com/daijro/camoufox).

This project supports grant research and application preparation. It does not guarantee eligibility, funding, or acceptance, and is not legal, tax, or financial advice. Confirm final requirements directly with each funder before submitting.
