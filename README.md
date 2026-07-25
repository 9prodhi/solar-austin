# Solar Austin Grant Search

An agent skill for finding and verifying institutional grant opportunities for
[Solar Austin](https://solaraustin.org/) and other Austin- or Texas-based
clean-energy nonprofits.

The core rule is simple:

> Grant databases produce leads. A lead becomes a recommendation only after the
> funder's own website has been checked in the current research session.

The skill turns prospecting data into a decision-ready, source-linked funder
dossier. It checks eligibility, deadlines, application paths, grant sizes,
contacts, and disqualifiers before recommending that anyone act.

## What the skill does

- Confirms the organization's nonprofit or fiscal-sponsor status and assigns an
  owner for outreach before suggesting applications.
- Finds prospects through the
  [open-grant-data MCP server](https://qwntl-labs-open-grant-data-mcp.hf.space/mcp).
- Treats database results as `UNVERIFIED` until each recommended funder is
  checked against its official website.
- Separates active and past funders, warm network prospects, new prospects, and
  ineligible organizations.
- Produces a compact dossier with an ask amount, timing, application path,
  sourced contact, and sequenced next actions.
- Re-verifies volatile facts such as deadlines and open cycles before an
  application is prepared.

## Why verification matters

Grant histories are useful for discovery, but they do not prove current
eligibility. A database can miss renewals, retain closed programs, or surface a
funder whose geography excludes Austin. This skill therefore requires:

1. A current visit to every recommended funder's official website.
2. A source URL for every deadline, dollar figure, and named contact.
3. A `verified-as-of` date on every dossier.
4. Fewer results—not weaker verification—when time is limited.

## Install

This repository contains a `SKILL.md` compatible with Claude Code's skill
format. Review the file before installing it; the declared tools include file,
shell, and web access.

Install it as a personal Claude Code skill:

```bash
git clone https://github.com/9prodhi/solar-austin.git \
  ~/.claude/skills/solar-austin-grant-search
```

Or install it only for the current project:

```bash
git clone https://github.com/9prodhi/solar-austin.git \
  .claude/skills/solar-austin-grant-search
```

Claude Code can select the skill automatically from its description. You can
also invoke it directly:

```text
/solar-austin-grant-search
```

## Configure the grant-data source

The grant graph is public and does not require an API key. If your agent
supports remote MCP servers, add this to the project's `.mcp.json`:

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

The skill uses the graph for prospecting and funder relationships. It relays
any caveat returned by the service verbatim and verifies all recommendations
independently.

## Example requests

```text
Who funds Austin nonprofits working on community solar and energy democracy?
```

```text
Create a verified funder dossier for Solar Austin. We have a fiscal sponsor,
and Maya owns outreach.
```

```text
Check whether the deadlines and application paths in our existing grant plan
are still current.
```

```text
Prepare a renewal approach for a past Solar Austin funder, including the ask,
timing, contact, and next three actions.
```

## Output

Every completed dossier follows the same structure:

1. **Research dates** — the live verification date and grant-graph snapshot
   date.
2. **Gate** — confirmation of nonprofit or fiscal-sponsor status and a named
   outreach owner.
3. **Tiered funder table** — `Funder | Verified relationship | The move |
   Ask $ | Timing | Contact (+ source)`.
4. **Action sequence** — numbered next steps, each assigned to an actor and a
   time bound.
5. **Sources** — page-level URLs for material facts.

Ineligible funders remain visible but are struck through with the specific
reason. A funder that is merely a poor fit is labeled deprioritized instead;
the two categories are not interchangeable.

## Local browser and evidence-base paths

`SKILL.md` currently includes two workspace-specific optimizations:

- A Camoufox CLI at
  `/workspace/camoufox-rs/target/release/camoufox` for JavaScript-heavy funder
  websites.
- An optional, previously verified evidence base at
  `/workspace/solar_aus/docs/funder-dossiers-full.md`, with a condensed example
  at `/workspace/solar_aus/docs/funder-dossier-solar-austin.md`.

Update those paths for your environment. If the evidence base is absent or
older than roughly 60 days, the agent should perform full official-site
verification. Static pages can be checked with ordinary web-fetch tools.

## Scope

Use this skill for:

- institutional funder prospecting;
- grant-deadline and eligibility checks;
- chapter or fiscal-sponsor funding;
- funder-specific application framing; and
- renewal planning for Austin and Texas clean-energy nonprofits.

It is not designed for individual-donor campaigns, general fundraising
strategy, or unmodified use outside Texas. The verification workflow is
portable, but the included funder knowledge is region-specific and
time-sensitive.

## Data freshness

Version 1.0 of the skill was verified on **July 25, 2026** and references a
**June 2026** grant-graph snapshot. Treat the funder framing in `SKILL.md` as a
starting point, not evergreen truth. Re-check it before outreach or submission,
especially when it is more than about 60 days old.

## Contributing

Changes should preserve the skill's non-negotiable safeguards:

- official-site verification for every recommendation;
- page-level sourcing for people, money, and dates;
- an explicit eligibility and ownership gate;
- honest separation of leads from verified recommendations; and
- a visible verification date.

When updating time-sensitive funder guidance, also update the
`verified-as-of` value in `SKILL.md`.

## Disclaimer

This project supports grant research and application preparation. It does not
guarantee eligibility, funding, or acceptance, and it is not legal, tax, or
financial advice. Confirm final requirements directly with each funder before
submitting.
