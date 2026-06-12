---
name: screening-and-shortlisting
user-invocable: false
description: Screens and shortlists Grasp tables/universes into qualified, prioritized, ranked, or top-N outputs across companies, buyers, targets, peers, competitors, acquirers, investors, or deals. Use when the user asks to screen, narrow, qualify, shortlist, prioritize, rank, select best/top candidates, map a landscape, or produce recommendations from a Grasp long list. Enforces coverage checks, enrichment/research/proxies, table links, and transparent escalation.
---

# Screening And Shortlisting

Use this skill to turn a Grasp source universe or long list into a qualified universe, shortlist, prioritized shortlist, ranked list, selected comps, or final recommendation. This is a Grasp workflow skill: use Grasp table inspection, coverage checks, enrichment, research columns, estimated financials, company lookup, and table links to make the screening process auditable.

Base cuts on table evidence, not preview rows, familiar names, ad hoc slices, or general knowledge.

## When To Use

Use after `creating-tables` or on an existing Grasp table when the user asks to screen, qualify, narrow, filter, shortlist, prioritize, rank, score, tier, select top-N, identify best/most relevant/most likely candidates, map a competitor landscape, refine a buyer universe, select precedent transactions, or produce a target/peer/buyer/deal recommendation.

If no table exists yet, first use `creating-tables` to create a high-recall universe, then return here.

## Industry Terms

- Source universe / long list: the starting Grasp table.
- Screening criteria: the user's in/out requirements.
- Qualification: deciding whether a row is actually in scope.
- Qualified universe: rows that pass the defensible in-scope screen.
- Shortlist: selected rows from the qualified universe.
- Prioritization / ranking / scoring: ordering or tiering the shortlist.
- Deep dive / company profile: closer inspection of important rows.

## Workflow

1. Inspect the universe.
   - Use `working-with-tables` to inspect metadata, row count, columns, table version, links, preview, and row/query/export limits.
   - Treat `grasp_get_table` preview rows as samples only.
   - Use UUID `table_id` handles for tool calls; `working-with-tables` can recover recent table IDs and conversation IDs when needed.
   - Every output or escalation must include the current Grasp table link.

2. Translate the mandate into screening criteria.
   - Separate hard filters from soft criteria.
   - Hard filters define in/out eligibility, such as geography, activity, entity type, deal date, buyer type, current ownership, or size.
   - Soft criteria drive prioritization, such as strategic fit, specialization, acquisition likelihood, quality of evidence, buyer relevance, or attractiveness.
   - If multiple interpretations are plausible and change the screen materially, pause and offer concrete Grasp-backed alternatives.

3. Check coverage before applying hard filters.
   - Use `working-with-tables` to describe/query coverage, missingness, distributions, and exact field values.
   - For every hard filter, account for how many rows are known in scope, known out of scope, and missing/uncertain.
   - Do not silently exclude blanks or unknowns. If missing values remain after filtering, state that they were not fully tested.

4. Add Grasp evidence before semantic or sparse-data cuts.
   - Use `table-enrichment` for source columns, estimated financials, research columns, computed fields, or company-level contact-like fields.
   - If the screen depends on semantic fit, specialization, customer segment, competitor relationship, buyer relevance, ownership/acquirability, likelihood to transact, or strategic rationale, add a narrow research column through `table-enrichment` before cutting.
   - If a financial screen depends on revenue, assets, employees, or headcount and coverage is sparse, use `table-enrichment` and its estimated-financials guidance before excluding rows.
   - Inspect enrichment results before filtering, scoring, or presenting.

5. Build the qualified universe.
   - Apply only defensible hard filters.
   - Keep uncertain rows in an explicit monitor/borderline bucket when data is incomplete but plausibly in scope.
   - Do not use keyword filters as a substitute for semantic fit research.
   - Do not select familiar names unless table evidence supports them.

6. Prioritize or rank only after qualification.
   - If the user supplies an already qualified set, start here after inspecting the row set, table link, and available evidence.
   - If eligibility is unresolved or new disqualifying concerns appear, return to qualification before final ranking.
   - Use simple, explainable dimensions derived from the mandate and table evidence.
   - Check legal entity and brand match, current parent or controlling owner when ownership matters, and scale versus mandate using returned or researched fields.
   - Separate clean fits, strategic exceptions, near misses, monitor rows, and excluded rows.
   - Label reported, estimated, researched, inferred, missing, and unverified evidence when it affects confidence.
   - Include concerns and near-miss reasons instead of hiding weak evidence behind a score.

7. Deep-dive important rows.
   - Use `company-lookup` for top candidates, expected-but-missing names, ambiguous identity, ownership checks, company profiles, financial detail, buyer capacity, or sanity checks.
   - For shortlisted buyers, use the `grasp_get_buyer_deals` tool with the row's buyer company_id to review the full deal history: acquisition cadence, typical deal sizes, and multiples paid where covered. Distinguish deal-count evidence from valuation evidence.
   - Claude's own knowledge and web research may validate or challenge Grasp evidence, but must not replace Grasp table screening for the primary cut.
   - Use `finding-contacts` only after the company/buyer shortlist is clear and the user explicitly asks for people, emails, outreach, or contacts.

## Coverage And Escalation

Pause or ask the user before making the cut when:

- A decisive hard filter has poor coverage.
- A filter would exclude many rows only because values are blank or unknown.
- The user asks for a revenue band but reported revenue coverage is sparse and estimated financials or proxies have not been added yet.
- Estimated financials/proxies still leave too much uncertainty for the requested confidence level.
- The mandate has multiple plausible interpretations that materially change the shortlist.
- The result is based on a sample, capped query, or partial export but the user expects full-universe confidence.
- Expected companies are missing and identity, scope, source, or filter causes are unclear.
- Research outputs are contradictory, low confidence, or too thin to support the cut.

When escalating, provide practical alternatives grounded in Grasp data and tools. Do not just say the task cannot be done.

Example escalation shape:

```text
Reported revenue coverage is 22%, so filtering to EUR 10-50m would silently drop the rows with missing revenue. Options:
1. Add estimated revenue and re-run the size screen.
2. Use employee count or LinkedIn headcount as a size proxy, then deep-dive borderline names.
3. Keep missing-revenue rows in a monitor bucket and only exclude companies with evidence they are outside the band.

Grasp table: [link]
```

## Required Output

When presenting a shortlist, ranking, filtered result, caveat, or escalation, include:

- The Grasp table link.
- Whether the conclusion is based on the full table, filtered rows, query result, export, or sample.
- A funnel summary: initial universe, hard-filter results, missing/uncertain rows, researched/enriched rows, qualified universe, shortlist/prioritized shortlist.
- The criteria used and which were hard filters versus soft ranking criteria.
- Rank/tier when relevant, plus evidence, concern, confidence, and Grasp/source links for shortlisted rows when available.
- Clear caveats for missing, estimated, inferred, stale, capped, or unverified evidence.

Use entity-appropriate final labels: prioritized target shortlist, priority buyer list, screened peer set, competitor landscape, selected precedent transactions, or prioritized shortlist.
