---
name: company-lookup
user-invocable: false
description: "Use when the current task is to resolve, validate, or analyze one specific company in Grasp: understanding a named target, buyer, reference, or sample company before a list, buyer, transaction, contact, or table workflow; fetching business, financial, ownership, employee, LinkedIn, deal, or similar-company profile sections; resolving ambiguous names/domains/org numbers; or checking whether a company exists in Grasp or may be missing from a table."
---

# Company Lookup

Use the `grasp_get_company` tool for one specific company. This is not a general company search tool.

## Use Cases

- Named-company context before a list workflow: target, buyer, reference company, or sample company that defines what to search for.
- One-company deep dives: business model, value chain, customers, competitive position, ownership, employees, financials, deal history, buyer capacity, or attractiveness.
- Ambiguous identity: company names/domains that may resolve to the wrong company.
- Missing-company diagnosis: checking whether a company exists in Grasp or why it may be absent from a table.

For list, buyer, competitor, target, peer, or transaction requests, use this skill as a supporting step and then return to the `creating-tables` skill.

## Lookup And Identity

1. Prefer an official website URL/domain from the user.
2. If the user gives an organization or registration number, pass `org_number` plus `country_iso2` to the `grasp_get_company` tool. Do not mix this lookup mode with `url`, `name`, or `country`.
3. If the user gives only a name, pass the name plus country or deal context to the `grasp_get_company` tool.
4. If lookup fails or identity is uncertain, try reasonable alternate identifiers. Use available web context to find an official domain or country context when needed.
5. Treat returned URL/domain, company ID, and organization number as canonical for later Grasp calls.
6. If multiple profiles could match, state the selected profile and ask for confirmation before using it as a table seed.

Do not invent canonical domains or organization numbers from memory.

## Depth And Output

Use shallow sections before table/list work. Request full sections only when needed for the user's question or next workflow.

- `output: "structured"`: use when the result feeds later tool calls, table creation, or analysis.
- `output: "text"`: use when the user only wants a readable one-company answer.
- `output: "both"`: use for deep dives where the user needs prose and the agent needs structured fields.
- `business`, `financials`, `employees`, `org_structure`: default partial sections are usually enough for mandate translation.
- `financials: "full"`: use for reported or estimated financial detail, evidence review, or acquisition-capacity analysis.
- `employees: "full"` or `linkedin: "partial" | "full"`: use for deeper headcount, LinkedIn, or geographic-presence analysis.
- `org_structure`, `shareholders`, `beneficial_owners`, `directors`: use for ownership, control, founder/family/PE/corporate status, or governance questions.
- `deals`: use for one-company profile deal history. For sector transaction comps or deal tables, use the `creating-tables` skill with transaction tables.
- `similar_companies`: use for profile-level peer hints or seed discovery, not as a substitute for a full company search.
- `contacts`: use only for company-profile contact records in a one-company deep dive when explicitly requested. For outreach/person search by role, use the `finding-contacts` skill.

Mark missing, estimated, stale, sparse, or converted fields as caveats.

## Answering

- For "what does X do?", cover business activity, products/services, geography, scale, ownership, and financials when returned.
- For fit, attractiveness, or buyer-capacity questions, separate returned facts from judgment.
- For comparisons, fetch each company separately and compare fields that were actually returned.
- For table/list setup, translate the company profile into a concise business activity, geography, scale, and canonical URL/domain for the next skill.

## Missing Company In A Table

Use this path when the user asks why an expected company is absent.

1. Validate the company with the `grasp_get_company` tool using URL/domain, org number plus `country_iso2`, or name plus country context.
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
