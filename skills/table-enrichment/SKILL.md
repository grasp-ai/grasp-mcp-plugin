---
name: table-enrichment
user-invocable: false
description: "Use when the user wants to add, enrich, fill, estimate, compute, classify, segment, or evaluate data on an existing Grasp table: financials, employees/headcount, ownership, locations, LinkedIn data, buyer metadata, deal values, transaction details, research columns, computed fields, company-level phone/email/website fields, or fit notes."
---

# Table Enrichment

Enrich only after inspecting the table. Prefer structured Grasp data before research questions, and distinguish table columns from separate contact lookup results.

This is an evidence-building skill. If enrichment is needed to narrow, qualify, shortlist, prioritize, or rank a universe, use the `screening-and-shortlisting` skill as the orchestrator and return there after inspecting enrichment results.

Existing-table enrichment uses UUID `table_id` values and does not take `conversation_id`. Do not pass `table_slug` as a tool handle. If enrichment requires creating helper/source tables that may later be composed, use the `creating-tables` skill first so those new tables share one setup `conversation_id`.

## Workflow

1. Inspect the table with the `grasp_get_table` tool.
2. Use the `grasp_describe_table` or `grasp_query_table` tool to understand coverage.
3. Prefer reported/source columns when Grasp already stores the field.
4. Use estimated financials only as labeled size proxies when reported coverage is thin.
5. Add research columns only for evidence that is not already structured and affects a decision.
6. Keep table contact-like fields separate from person-level outreach contacts.
7. Inspect enrichment results before filtering, scoring, or presenting.

Do not make enrichment coverage claims from a `grasp_get_table` preview alone; use rows, describe, or query to inspect the affected columns.

When research is needed, plan one `grasp_add_research_column` step, inspect coverage/results, then decide whether another is needed.

When several related qualitative criteria support one decision, prefer one decision-evidence research column over several separate upfront research columns.

## Tool Choice

- One explicit research column: use the `grasp_add_research_column` tool.
- Multiple table workflow changes, filters, sort, layout, or compute: use the `grasp_update_table` tool.
- Person-level outreach contact lookup: use the `finding-contacts` skill and keep contact lookup results separate from table fields in the answer.
- Schema uncertainty: use the `grasp_read_docs` tool.

Always include a short `version_summary` for table updates.

## Source Columns

Use source columns for fields Grasp already stores: financials, employees, ownership, locations, LinkedIn, buyer metadata, deal values, transaction details, company-level phone, website, generic email, or company LinkedIn fields.

- Inspect existing columns first.
- Use the `grasp_read_docs` tool before adding unfamiliar fields.
- Prefer reported structured fields over research columns when they answer the question.
- Check coverage before treating a field as a filter, screening criterion, shortlisting criterion, or ranking criterion.
- Do not guess currency suffixes or entity-specific field names.
- Company-level phone, generic email, website, or company LinkedIn fields are source data when Grasp documents them as data columns; they are not person-level outreach contacts.
- Use the `grasp_update_table` tool with `workflow.data_columns.add` when adding columns to a persisted table.

## Estimated Financials

Label estimates; check coverage before filtering.

- Inspect returned columns before using revenue, assets, employee, or currency fields.
- Prefer reported/latest available fields when returned.
- Use estimates only as a size proxy and label them as estimates.
- Keep currency suffixes visible in analysis.
- Check non-null coverage before filtering, screening, shortlisting, or ranking by financials.
- If coverage is thin, present the gap and consider employee count, ownership, or research as proxies.
- Use the `grasp_read_docs` tool with the relevant topic when exact data-column names are uncertain.

## Research Questions

Research questions answer evidence questions for companies in a table and add one column per question. Use them for evidence that is not already structured and affects a decision.

Good question shapes:

- Binary: "Does the company provide rodent control services?"
- Categorical: "What is the company's primary customer segment: residential, commercial, industrial, or mixed?"
- Extraction: "What evidence is available for the company's current owner?"
- Free text: "Summarize the evidence for why this company fits the mandate."

Guidelines:

- Ask one narrow question per research column.
- When several related qualitative criteria support one decision, prefer one decision-evidence question over several separate upfront research columns.
- Prefer binary or categorical when the result drives filters.
- Use `where` to limit research to eligible rows when possible.
- Do not use research as a substitute for source columns.
- Treat research outputs as evidence to review, not automatic truth.
- Treat contact-like research as evidence to verify, not confirmed outreach contacts.

## Contact-Like Table Fields

Use only when the user wants contact-like information persisted as table columns. For person-level outreach contacts, stop and use the `finding-contacts` skill.

Do not treat a ranking, shortlist, PE add-on, acquisition-target, or buyer-universe request as a contact request unless the user explicitly asks for outreach contacts.

- Source columns can provide company-level phone, website, generic email, or company LinkedIn fields when Grasp documents them as available table columns.
- Research columns can provide broad public extraction at table scale when the user needs contact-like evidence across many companies.
- These are not contact lookup results and should not be presented as verified outreach contacts.
- If the user wants CEO/CFO/founder/owner/corporate-development people, work emails, phone numbers, person LinkedIn profiles, or outreach routes for selected companies, use the `finding-contacts` skill.
- If the user asks for contacts across a full long list, recommend narrowing first. If they still need table-wide coverage, use research columns and state that the result is contact-like evidence, not contact lookup data.
- Inspect exact URL/domain columns before adding any contact-like research column.
- Present missing values as missing coverage, not as proof that no relevant contact exists.
