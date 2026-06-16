# Skill: Account-list-building 
description: 
Build a targeted account list from an ICP definition — translate ICP criteria into source-specific search filters, pull and dedupe candidate companies, enrich them, and output a clean list ready to score. Use this skill whenever the user wants to build, source, or refresh a target account list, a TAM/SAM list, an outbound list, or an ABM target list; whenever they ask "who should we go after," "find companies that match our ICP," "build me a prospect list," or want to feed accounts into the ICP scoring skill. Trigger even if they don't say "list" — sourcing accounts, finding lookalikes of best customers, or turning an ICP into actual companies all belong here.
Skill: Account List Building
Duration: 30–60 minutes per list (or run in batch across segments)
Output: Clean, deduped, enriched account list saved to outputs/account-lists/, formatted to feed directly into the ICP Scoring skill

Quick Start
Single segment:
Read skills/account-list-building/SKILL.md and context/icp-definition.md.
Build a target account list for our ICP. Output a deduped table I can score.

From best customers (lookalike):
Read skills/account-list-building/SKILL.md and context/icp-definition.md.
Here are our 15 best customers: [paste]. Find lookalike accounts and output a scored-ready list.

Signal-first (timely outbound):
Read skills/account-list-building/SKILL.md, context/icp-definition.md, and context/signal-library.md.
Build a list of accounts that fit our ICP AND triggered a signal in the last 30 days.


Purpose
Turn an ICP definition into a list of companies worth pursuing. Scoring tells you which accounts to prioritize; this skill produces the accounts to score in the first place. A good list is the difference between a rep working 200 well-chosen accounts and 2,000 random ones.
This skill ends where the ICP Scoring skill begins: its output is formatted as the scoring skill's input, so the handoff is one step.

When to Run This Skill
Standing up a new outbound or ABM motion and you have no list yet
ICP definition was updated — rebuild the list to capture newly qualifying companies
A rep or segment needs fresh accounts to work this quarter
A new campaign needs a purpose-built list (industry, region, persona, or trigger)
Existing list is stale, over-worked, or full of accounts that never converted
You have a strong set of closed-won customers and want lookalikes

Inputs
context/icp-definition.md — the firmographic, technographic, and organizational criteria (same file the scoring skill uses)
context/signal-library.md — only needed for signal-first builds
Capacity number — how many accounts the team can realistically work this period. Size the list to this, not to the whole market.
Suppression inputs — current customers, open opportunities, active pipeline, competitors, do-not-contact. The list must exclude these.
(Lookalike builds only) a seed set of 10–20 best-fit customers
If any of these are missing, ask for them before building — especially capacity and suppression. A list that ignores capacity or re-surfaces existing customers wastes rep time. However, provide the option to the user that if any of them is missing, you can skip and progress to the list building phase. 

Step 1: Pick a Build Strategy
Choose based on how well-defined the ICP is and how time-sensitive the motion is.
Strategy
Best when
How it works
Top-down (filter)
ICP is well-defined firmographically
Start from the full market universe, apply ICP filters to narrow down
Lookalike (bottom-up)
ICP is fuzzy, or relationships/feel matter more than firmographics
Start from best customers, find companies that resemble them
Signal-first
Timely outbound; you want accounts acting now
Start from a trigger event, then filter survivors for ICP fit

These combine well: build top-down for coverage, then layer signal-first on top to decide sequencing. State which strategy you're using at the top of the output so the list is interpretable later.

Step 2: Translate ICP Criteria into Search Filters
This is the core of the skill. Each ICP criterion maps to a data source and a concrete filter. Read context/icp-definition.md and build the mapping before pulling any companies — don't search blindly.
ICP criterion (from definition)
Where to find it
How to filter
Employee count / company size
Sales Nav, Apollo, Crunchbase, ZoomInfo
Headcount range filter
Industry / vertical
Sales Nav, Apollo, Crunchbase
Industry codes; verify against the actual business, not just the label
Funding stage / revenue
Crunchbase, PitchBook, public filings
Last-round stage or revenue band
Geography
Any firmographic source
HQ country/region; note if subsidiaries count
Technographic (uses tool X)
BuiltWith, Wappalyzer, HG Insights, Datanyze
Detected-technology filter
Org structure (has role/team)
LinkedIn, Sales Nav
Title search within the company
Hiring signal (hiring for role)
Job boards, Greenhouse/Lever pages, LinkedIn Jobs
Open-req search by title


Buyer contact
LinkedIn, Sumble, Apollo, Clay
Buyer persona based on title and type


Sources are examples — substitute whatever the team actually has access to. The principle holds regardless of tool: every ICP criterion needs a corresponding filter, and any criterion you can't filter on becomes a manual verification step later (flag these as “unfiltered”).
Translate, don't loosen. If the ICP says "Series B–C SaaS, 200–800 employees, using Snowflake," the list should match all three, not "SaaS companies, roughly mid-size." Each dropped criterion multiplies noise the scoring skill then has to filter back out.

Step 3: Pull, Dedupe, and Normalize
Pull candidate companies and the appropriate contacts from each source per the filters above.
Dedupe across sources. Match on domain first (most reliable), then normalized company name. Subsidiaries and rebrands cause duplicates — collapse them to the parent unless the ICP treats divisions separately. Flag these as “deduped” so that the user is aware of your actions. 
Normalize fields to a consistent shape: canonical company name, root domain (no www, no path), headcount as a number, industry to your taxonomy.
Suppress anything on the suppression list — current customers, open opps, competitors, do-not-contact. Do this last so suppressed accounts don't silently survive a dedupe merge.

Step 4: Enrich for Scoring
The scoring skill needs firmographic, technographic, and organizational fields. Populate as many as the sources allow so accounts arrive at scoring ready to evaluate, not half-blank.
At minimum, try to fill the columns scoring reads:
Firmographic: employee count, industry, funding stage, geography
Technographic: key tool(s) detected, disqualifying tool present (yes/no)
Organizational: key role present (yes/no), recent leadership hire, relevant open roles
Leave a field blank rather than guessing. A blank tells the scorer "unknown"; a wrong value silently corrupts the score. Mark fields that need manual verification.

Step 5: Size to Capacity
Compare the list length to the capacity number from Inputs.
List ≤ capacity: good. If well under, consider relaxing a secondary criterion (never a core one) or adding an adjacent segment.
List ≫ capacity: don't hand over a list nobody can work. Either narrow the ICP filters, split into prioritized waves, or let the scoring skill rank and take the top N. Note in the output how you trimmed.

Output Format
Save to outputs/account-lists/[segment]-[YYYY-MM-DD].md (and/or CSV). The table columns are deliberately the scoring skill's input fields, so scoring runs directly on this file.
# Account List: [Segment Name]
Built: [YYYY-MM-DD]
Strategy: [Top-down / Lookalike / Signal-first]
ICP version: [date or version of icp-definition.md used]
Sources: [list]
Suppressions applied: [customers / open opps / competitors / DNC]
Count: [N] (capacity target: [M])

| Company | Domain | Employees | Industry | Funding stage | Geo | Key tech | Disqualifying tech | Key role present | Recent leadership hire | Relevant open roles | Signal (if any) | Source | Notes / needs verification |
|---------|--------|-----------|----------|---------------|-----|----------|--------------------|------------------|------------------------|---------------------|-----------------|--------|----------------------------|
| | | | | | | | | | | | | | |

## Build Notes
- How the list was filtered and anything relaxed or trimmed to hit capacity
- Criteria that couldn't be filtered automatically (manual verification needed)
- Segments intentionally excluded

After producing the list, point the user to the next step:
List ready at outputs/account-lists/[file]. To prioritize it, run:
Read skills/icp-scoring/SKILL.md and context/icp-definition.md, then score this list.


Quality Checks Before Handoff
Run these before declaring the list done:
Spot-check 5 random accounts against the ICP by hand. If any obviously don't fit, a filter is too loose — fix it and re-pull rather than shipping noise.
Domain coverage: every row has a valid root domain (scoring and enrichment both key off it).
No suppressed accounts survived the dedupe merge.
Duplicate scan on domain — zero exact duplicates.
Count vs capacity is reasonable, and any trimming is documented.

Common Pitfalls
Industry-label trap: source industry tags are noisy. A company tagged "Software" may be an IT reseller. Verify the actual business for borderline rows.
Over-loosening to hit a number: padding the list with marginal accounts just moves the filtering burden to scoring and the rep. Fewer, tighter accounts win.
Skipping suppression: re-surfacing current customers or open opps erodes trust in the list immediately.
Guessed enrichment: a confidently wrong field is worse than a blank one.
Building past capacity: a 5,000-account list for a team that can work 250 isn't a list, it's a backlog.

Refinement Notes
Update this section as you learn which list-building choices produced accounts that actually converted.
Date
Segment
Strategy
Sources
Convert rate vs other lists
What to change next build













Review quarterly alongside the scoring skill's calibration: if a whole segment scores high but never converts, the list-building filters (not the scoring model) may be the problem.

