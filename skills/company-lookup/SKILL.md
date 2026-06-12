---
name: company-lookup
user-invocable: false
description: "Use when the current task is to resolve, validate, or analyze one specific company in Grasp: understanding a named target, buyer, reference, or sample company before a list, buyer, transaction, contact, or table workflow; fetching business, financial, ownership, employee, LinkedIn, deal, or similar-company profile sections; resolving ambiguous names/domains/org numbers; or checking whether a company exists in Grasp or may be missing from a table."
---

# Company Lookup

Use two tools together for one specific company: `grasp_search_companies` to resolve identity into a `company_id`, then `grasp_get_company` to fetch that entity. Neither is a general company-list tool.

## Use Cases

- Named-company context before a list workflow: target, buyer, reference company, or sample company that defines what to search for.
- One-company deep dives: business model, value chain, customers, competitive position, ownership, employees, financials, acquisition history, buyer capacity, or attractiveness.
- Ambiguous identity: company names/domains that may resolve to the wrong legal entity.
- Missing-company diagnosis: checking whether a company exists in Grasp or why it may be absent from a table.

For list, buyer, competitor, target, peer, or transaction requests, use this skill as a supporting step and then return to the `creating-tables` skill.

## Resolve Identity First

`grasp_get_company` accepts only a `company_id`. Resolve it before fetching:

1. If a table row already provides a company-id column, use that id directly and skip search.
2. Otherwise call `grasp_search_companies` with what the user gave: free-text `query` for names and/or website domains, `org_number` for organization/registration numbers, and `countries` to narrow when the country is known.
3. Read the candidates as legal entities, not interchangeable matches. The same brand often resolves to many entities across countries (operating companies, holdcos, country subsidiaries). Pick using name, url, org_number, country, and company_size together.
4. If candidates are ambiguous and the choice affects the answer, state the selected entity and ask for confirmation before deep work or table seeding. Do not silently pick.
5. If `total_hits` exceeds the returned candidates, refine the query, add `countries`, or use `org_number` before picking.
6. If search finds nothing, try reasonable brand, legal-name, domain, and org-number variants. Use available web context to find an official domain when needed.
7. Treat the returned `company_id`, URL/domain, and organization number as canonical for later Grasp calls in this session. Company ids are not stable long-term: resolve them fresh rather than reusing ids remembered from earlier sessions.

Org numbers are matched exactly (with spacing/casing tolerance) and are not globally unique; pair them with `countries` when possible. Do not invent canonical domains or organization numbers from memory.

## Depth And Output

Use shallow sections before table/list work. Request full sections only when needed for the user's question or next workflow.

- `output: "structured"`: use when the result feeds later tool calls, table creation, or analysis.
- `output: "text"`: use when the user only wants a readable one-company answer.
- `output: "both"`: use for deep dives where the user needs prose and the agent needs structured fields.
- `business`, `financials`, `employees`, `org_structure`: default partial sections are usually enough for mandate translation.
- `financials: "full"`: use for reported or estimated financial detail, evidence review, or acquisition-capacity analysis.
- `employees: "full"` or `linkedin: "partial" | "full"`: use for deeper headcount, LinkedIn, or geographic-presence analysis.
- `org_structure`, `shareholders`, `beneficial_owners`, `directors`: use for ownership, control, founder/family/PE/corporate status, or governance questions.
- `deals`: returns an acquisition-history summary only (total deal count, latest deal year). Use it to learn whether and how recently the company acquires. For the complete deal history of this company as a buyer, including deal values and valuation multiples, use the `grasp_get_buyer_deals` tool with the same company_id. For sector transaction comps driven by target type, use the `creating-tables` skill with transaction tables.
- `similar_companies`: use for profile-level peer hints or seed discovery, not as a substitute for a full company search.
- `contacts`: use only for company-profile contact records in a one-company deep dive when explicitly requested. For outreach/person search by role, use the `finding-contacts` skill.

Mark missing, estimated, stale, sparse, or converted fields as caveats.

## Buyer Deal History

For "what has X acquired", acquisition cadence, typical deal sizes, or multiples paid, call the `grasp_get_buyer_deals` tool with the company's company_id after resolving identity. This is a lookup with full recall over that buyer's recorded deals, returned newest first with pagination.

- Read the coverage summary first: deal sizes, enterprise values, and multiples exist only for a subset of deals. Distinguish deal-count evidence from valuation evidence.
- Multiples carry derivation types (Reported, Implied, Calculated); qualify valuation claims accordingly. Monetary values are USD.
- Deal records embed target identity, description, and key financials for in-context resolution. For deeper target detail when available — full financials, ownership, employees — call `grasp_get_company` with the deal's `target_company_id`.
- Deals have no stable ids. Reference them by target and year.
- Do not use transaction tables for one known buyer's history; they are semantic search by target type and do not guarantee complete recall for a single buyer.

## Answering

- For "what does X do?", cover business activity, products/services, geography, scale, ownership, and financials when returned.
- For fit, attractiveness, or buyer-capacity questions, separate returned facts from judgment.
- For comparisons, resolve and fetch each company separately and compare fields that were actually returned.
- For table/list setup, translate the company profile into a concise business activity, geography, scale, and canonical URL/domain for the next skill.

## Missing Company In A Table

Use this path when the user asks why an expected company is absent.

1. Resolve the company with `grasp_search_companies` using URL/domain, org number, or name plus country variants, then fetch it with `grasp_get_company`.
2. If the user did not provide an official URL/domain, search enough to find one before concluding the company is absent.
3. Try reasonable brand, legal-name, domain, org-number, and country variants.
4. Confirm whether the table's stated scope should include that company.
5. Use the `working-with-tables` skill to inspect table metadata, rows, filters, geography, source criteria, and semantic research columns.
6. Check likely causes before concluding:
   - already present under another name, URL, or parent
   - hidden by current filters or sort
   - outside geography, size, ownership, or activity scope
   - source criteria too narrow
   - missing from available Grasp company data
7. If it should be explicitly included in a new/corrected table, use the validated URL in `entities` when that matches the user's intent. Use `similar_companies` only for peer/similar-company seeding.

Do not claim Grasp has no record of a company unless URL, org-number, name, and country variants support that conclusion. Prefer "not found after these lookups" when evidence is limited.

## Follow-Up Routing

- Similar companies, peers, competitors, targets, or explicit company seeds: use the `creating-tables` skill.
- Existing table screening, qualification, prioritization, ranking, or shortlisting: use the `screening-and-shortlisting` skill.
- Existing table filtering or missing-row diagnosis after lookup: use the `working-with-tables` skill.
- Table columns or research about multiple companies: use the `table-enrichment` skill.
- Person-level outreach contacts: use the `finding-contacts` skill.
