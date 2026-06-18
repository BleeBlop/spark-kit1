# Skill: Account-list-build 

**Duration:** 30–60 minutes per list (or run in batch)
**Output:** Clean, deduped, enriched account list saved to CRM or 'outputs/account-lists/'

---

## Quick Start
```
Read skills/account-list-build/SKILL.md and context/icp-definition.md and output a table with a target account list against our ICP. 
```


---
## Purpose
Turn an ICP definition into a list of companies worth pursuing. When run at scale it produces the accounts to score rather than manually searching for accounts.  A good list is the difference between a rep working 200 well-chosen accounts and 2,000 random ones.

---

## When to Run This Skill

- Launching a new outbound or ABM motion and you have no list yet
- ICP definition was updated — capture newly qualifying companies
- A rep or segment needs fresh accounts to work this quarter
- A new campaign needs a purpose-built list (industry, region, persona, or trigger)
- You have a strong set of closed-won customers and want lookalikes

---

## Inputs
- context/icp-definition.md — the firmographic, technographic, and organizational criteria (same file the scoring skill uses)
- Suppression inputs — current customers, open opportunities, active pipeline, competitors, do-not-contact. The list must exclude these.
- If any of these are missing, ask for them before building. However, provide the option to the user that if any of them is missing, you can skip and progress to the list building phase. 

---


## Step 2: Build list and translate ICP criteria into search filters

This is the core of the skill. Each ICP criterion maps to a data source and a concrete filter. Read context/icp-definition.md and build the mapping before pulling any companies — don't search blindly.


| ICP criterion (from definition) | Where to find it | How to filter |
|---------|-----------|-----|
| Employee count / company size | Sales Nav, Apollo, Crunchbase, ZoomInfo | Headcount range filter |
| Industry / vertical | Sales Nav, Apollo, Crunchbase | Industry codes; verify against the actual business, not just the label |
| Funding stage / revenue | Crunchbase, PitchBook, public filings | Last-round stage or revenue band |
| Geography | Any firmographic source | HQ country/region; note if subsidiaries count |
| Technographic (uses tool X) | BuiltWith, Wappalyzer, HG Insights, Sumble | Detected-technology filter |
| Org structure (has role/team) | LinkedIn, Sales Nav | Title search within the company |
| Hiring signal (hiring for role) | Job boards, Greenhouse/Lever pages, LinkedIn Jobs | Open-req search by title |
| Buyer contact names | LinkedIn, Sumble, Apollo, Clay | Buyer persona based on title and type |
| Website/URL | Google Search | Indicate whether geo specific |


Sources are examples — substitute whatever the team actually has access to. The principle holds regardless of tool: every ICP criterion needs a corresponding filter, and any criterion you can't filter on becomes a manual verification step later (flag these as “unfiltered”).
Translate, don't loosen. If the ICP says "Series B–C SaaS, 200–800 employees, using Snowflake," the list should match all three, not "SaaS companies, roughly mid-size." Each dropped criterion multiplies noise the scoring skill then has to filter back out.

---

## Step 2: Pull, dedupe, and normalize

- Pull candidate companies and the appropriate contacts from each source per the filters above.
- Dedupe across sources. Match on domain first (most reliable), then normalized company name. Subsidiaries and rebrands cause duplicates — collapse them to the parent unless the ICP treats divisions separately. Flag these as “deduped” so that the user is aware of your actions.
- Normalize fields to a consistent shape: canonical company name, root domain (no www, no path), headcount as a number, industry to your taxonomy.
- Exclude anything on the suppression list — current customers, open opps, competitors, do-not-contact. Do this last so suppressed accounts don't silently survive a dedupe merge.

---

## Step 3: Enrich for scoring

- The scoring skill needs firmographic, technographic, and organizational fields. Populate as many as the sources allow so accounts arrive at scoring ready to evaluate, not half-blank.
- At minimum, try to fill the columns scoring reads:
- Firmographic: employee count, industry, funding stage, geography
- Technographic: key tool(s) detected, disqualifying tool present (yes/no)
- Organizational: key role present (yes/no), recent leadership hire, relevant open roles
- Leave a field blank rather than guessing. A blank tells the scorer "unknown"; a wrong value silently corrupts the score. Mark fields that need manual verification.

---

## Step 4: Size to capacity

- If capacity number for the list is provided, compare the list length to the capacity number from Inputs. Otherwise, you can skip this step.
- List ≤ capacity: good. If well under, consider relaxing a secondary criterion (never a core one) or adding an adjacent segment.
- List ≫ capacity: don't hand over a list nobody can work. Either narrow the ICP filters, split into prioritized waves, or let the scoring skill rank and take the top N. Note in the output how you trimmed.

## Output format

- Save to outputs/account-lists/[company name]-[YYYY-MM-DD].md (and/or CSV). The table columns are deliberately the scoring skill's input fields, so scoring runs directly on this file.
- Built: [YYYY-MM-DD]
- ICP version: [date or version of icp-definition.md used]
- Sources: [list]
- Suppressions applied: [customers / open opps / competitors / DNC]
- Count: [N] (capacity target: [M])

| Company | Domain | Employees | Industry | Funding stage | Geo | Key tech | Disqualifying tech | Key role present | Recent leadership hire | Relevant open roles | Signal (if any) | Source |  Notes / needs verification | Buyer contacts |
|---------|--------|-----------|----------|---------------|-----|----------|--------------------|------------------|------------------------|---------------------|-----------------|--------|-----------------------------|----------------|
| | | | | | | | | | | | | | | |

---

## Build notes
- How the list was filtered and anything relaxed or trimmed 
- Criteria that couldn't be filtered automatically (manual verification needed)
- Segments intentionally excluded

---

After producing the list, point the user to the next step:
List ready at outputs/account-lists/[file]. To prioritize it, run:
Read skills/icp-scoring/SKILL.md and context/icp-definition.md, then score this list.

---

## Quality checks before handoff

Run these before declaring the list done:
- Spot-check 5 random accounts against the ICP by hand. If any obviously don't fit, a filter is too loose — fix it and re-pull rather than shipping noise.
- Domain coverage: every row has a valid root domain (scoring and enrichment both key off it).
- No suppressed accounts survived the dedupe merge.
- Duplicate scan on domain — zero exact duplicates.

---

## Common pitfalls

- Industry-label trap: source industry tags are noisy. A company tagged "Software" may be an IT reseller. Verify the actual business for borderline rows.
- Over-loosening to hit a number: padding the list with marginal accounts just moves the filtering burden to scoring and the rep. Fewer, tighter accounts win.
- Skipping suppression (if suppression criteria is provided): re-surfacing current customers or open opps erodes trust in the list immediately.
- Guessed enrichment: a confidently wrong field is worse than a blank one.
- Building past capacity (if capacity threshold is provided): a 5,000-account list for a team that can work 250 isn't a list, it's a backlog.

---

## Refinement Notes

Update this section as you learn which list-building choices produced accounts that actually converted.
| Date | Segment| Strategy | Sources | Convert rate vs other lists | What to change next build |
|------|---------|--------|---------------|----------------------|----------------------------|
| | | | | | |



Review quarterly alongside the scoring skill's calibration: if a whole segment scores high but never converts, the list-building filters (not the scoring model) may be the problem.

