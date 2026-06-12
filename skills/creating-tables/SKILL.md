---
name: creating-tables
user-invocable: false
description: Use when the user wants to find, build, generate, source, map, or import a long list/source universe of companies, targets, peers, competitors, buyers, acquirers, investors, or M&A/deal transactions. Creates or imports the initial Grasp table, then waits, inspects, and hands off to screening-and-shortlisting, table enrichment, table work, or contacts as needed.
---

# Creating Tables

Create the initial Grasp evidence set, wait for completion, inspect it, then hand off. Do not pack screening, filtering, ranking, enrichment, or research into the initial table unless the user explicitly asks for a narrow source.

For requests that span table creation plus screening, filtering, enrichment, ranking, prioritization, shortlisting, or contacts, use this skill for the table-creation step, then hand off to the relevant dedicated skill.

## General Workflow

1. Choose the table path below and translate the mandate into activity, geography, entity type, and optional seed companies.
2. When the request depends on a named target, buyer, reference company, or sample company, use the `company-lookup` skill first to understand the company's business, scale, geography, and canonical URL/domain, then return here to create the table.
3. If this table-building scope does not already have a `conversation_id`, or the user starts a new scope or asks for a new Grasp conversation, call `grasp_setup_conversation`; then create/import/compose the table with that `conversation_id` and wait for completion.
4. Inspect with the `grasp_get_table`, `grasp_get_table_rows`, and, when needed, `grasp_describe_table` tools.
5. For screening, qualification, prioritization, top-N, ranking, or shortlist presentation from the long list, use the `screening-and-shortlisting` skill.
6. For filtering, sorting, querying, exporting, or missing-row diagnosis without analyst selection, use the `working-with-tables` skill.
7. For source columns, estimated financials, research columns, computed fields, and table-facing contact requests, use the `table-enrichment` skill.
8. For person-level contacts, use the `finding-contacts` skill only after the target companies or buyers are clear.

The default table-creation sequence is the `grasp_setup_conversation` tool for a new table-building scope, the `grasp_create_table` tool with the returned `conversation_id`, the `grasp_check_job_status` tool to poll the async job if needed, the `grasp_get_table` tool, then row/describe/query inspection. Treat `grasp_get_table` previews as samples; do not make full-table, top-N, or coverage claims until rows, describe, query, or export support them. Do not call the `grasp_add_research_column` tool before that inspection.

## Conversation Context

New table artifacts need a `conversation_id`. Set it once per table-building scope, not once per create/import/compose call.

- Before the first `grasp_create_table` or `grasp_import_table` call in a table-building scope, call `grasp_setup_conversation` once and reuse the returned `conversation_id` for every new table artifact in that scope.
- If the user asks for separate tables that may later be compared, merged, deduped, or composed, create all of those source tables with the same `conversation_id`.
- For `grasp_compose_table`, use the `conversation_id` shared by the source tables. If those source tables were created in the current task, this is the setup ID. If they already exist, recover their `conversation_id` with `grasp_list_tables`, `grasp_get_table`, or `grasp_check_job_status`, and compose only tables from the same conversation.
- Use UUID `table_id` values returned by status/list/get tools. Do not pass `table_slug` as a tool handle; slugs are display, navigation, and SQL identifiers only.
- Existing-table update, research, enrichment, row-read, query, describe, and export tools derive conversation context from UUID `table_id` and do not take `conversation_id`.

## Company Universes

Use `grasp_create_table` with `entity_type: "company"` for targets, peers, competitors, vendors, service providers, and market maps.

- Use `business_activity` for the operating activity of the companies to find, e.g. "pest control services" or "industrial MRO distributors".
- Put countries or regions in the `geography` field.
- Leave size, revenue, ownership, exclusions, screening, shortlisting, and ranking for supported source filters or follow-up table work after creation.
- Use validated company URLs in `similar_companies` when the user wants peers, competitors, targets, or similar companies around a seed.
- Use explicit `entities` only when the user wants specific companies included, not as a substitute for a broad market source.
- Prefer recall at creation, then refine with screening, filters, research, enrichment, or ranking.
- Inspect exact returned columns before filtering on employees, revenue, ownership, industry, or location.
- Add semantic research for fit questions such as specialization, product/service mix, commercial segment, or false-positive exclusion.
- For shortlists, top-N, prioritization, or ranked outputs, use `screening-and-shortlisting` after creation.

Example: "Find Nordic pest control providers for Anticimex, 10-75 employees, specializing in rodent control."

- `business_activity`: "Pest control services"
- `geography`: Sweden, Norway, Denmark, Finland
- After creation: inspect available fields; add one narrow decision-evidence research column only when needed.
- If more qualitative evidence is needed, scope follow-up research to the narrowed set.

## Buyer Universes

Use `grasp_create_table` with `entity_type: "buyer"` for strategic buyers, financial buyers, acquirers, investors, and buyer universes.

- Use `business_activity` for the type of company the buyer should acquire or invest in. Do not describe buyer attributes there.
- Use `geography` for buyer geography: buyers headquartered in that geography or buyers with acquisition/investment history there.
- Leave buyer size, ownership, screening, ranking, and exclusions for supported source filters or follow-up table work after creation.
- If the user explicitly asks for buyers headquartered in X, create the buyer table, inspect returned buyer HQ/location columns, then filter after confirming exact columns.
- If the user cares about "Nordic exposure" or "European software acquisition history", use geography to capture that exposure rather than stuffing it into `business_activity`.
- Do not restrict buyer type by default. If requested, use `grasp_read_docs` to confirm the supported buyer-type filter; do not pass buyer type as a top-level create-table field.
- Use transaction tables when the user needs returned transaction evidence or wants acquirer activity independent of buyer-search ranking.
- After creation, inspect the buyer table before filtering, enrichment, screening, shortlisting, or ranking.
- If the user asks for buyer prioritization, top-N, rationale, or a buyer shortlist, use the `screening-and-shortlisting` skill. It will call `table-enrichment` when buyer-fit evidence is missing.
- Do not search contacts during buyer-table creation. If the user explicitly asks for outreach contacts after buyers are selected, use the `finding-contacts` skill.

## Transaction Universes

Use `grasp_create_table` with `entity_type: "transaction"` for semantic transaction discovery by target type: precedent transactions, sector acquisition activity, market consolidation, and transaction comps.

- Transaction tables are search, not lookup. For the complete deal history of one known buyer ("what has buyer X acquired"), use the `grasp_get_buyer_deals` tool with the buyer's company_id instead; search recall is not guaranteed complete for a single buyer.
- Use `business_activity` for the target company type in the transactions. Do not describe buyer/acquirer attributes there.
- Use `geography` for target-company geography in the transactions. It is not buyer/acquirer HQ geography.
- Leave buyer type, deal-size, screening, ranking, and exclusions for supported source filters or follow-up table work after creation.
- Use one transaction table for a coherent multi-region scope such as US and Europe when Grasp can express it. Split into separate tables only if the user asks for separate analyses, the tool cannot express the combined geography, or inspection shows caps/coverage require a follow-up split.
- If the user wants acquirers from a geography, create/query transaction rows first, inspect buyer/acquirer fields, then filter or aggregate after creation.
- Add year constraints with supported source filters when docs confirm the shape; otherwise filter after creation.
- Inspect target, buyer, year, country, deal status, value, and source fields before drawing conclusions.
- Use the `grasp_query_table` tool for buyer frequency, active acquirers, buyer-type mix, target countries, deal years, and available value coverage.
- For selected comps, precedent-transaction shortlists, top-N, or ranked deal outputs, use `screening-and-shortlisting` after creation.
- Do not create a buyer table just because the user asks about active buyers or buyer types in a transaction comparison workflow. Create a buyer table only when they ask for a standalone buyer universe, buyer outreach, or buyer profiles beyond the transaction evidence.
- Do not imply valuation coverage if values or multiples are sparse.
- Distinguish deal-count evidence from valuation evidence.

## User-Provided Lists

Use `grasp_import_table` when the user brings their own CSV/Excel/copied company rows, URLs, domains, organization numbers, or names.

- Call `grasp_setup_conversation` first, then pass the returned `conversation_id` to `grasp_import_table`.
- For CSV/Excel, read the file in the host environment and convert rows to objects.
- Preserve useful user columns as source context.
- Prefer URL/domain columns for matching when present.
- If no URL/domain exists, use organization/registration number with a country or country_iso2 column.
- If neither URL nor organization number exists, use company name with country/location hints.
- Pass explicit column_mapping values:
  - url for website/domain columns.
  - org_number plus country or country_iso2 for registration-number matching.
  - name plus optional country/location for name matching.
- Normalize obvious formatting issues without changing company meaning, such as whitespace, URL schemes, `www.` prefixes, trailing paths, casing, or registration-number punctuation.
- Name-based matching is weaker than URL/domain matching; after import, inspect match quality before filtering or shortlisting.
- Do not use the `grasp_create_table` tool for a user-owned list unless the goal is to create a new Grasp-sourced universe.
- After import, wait for completion, inspect match quality, then use `screening-and-shortlisting`, `working-with-tables`, `table-enrichment`, or `finding-contacts` as needed.

## Compose Existing Tables

Use `grasp_compose_table` when the user wants a merged, compared, deduplicated, or composed Grasp table from existing tables.

- Use the `grasp_list_tables` tool to find recent candidates when the user does not provide handles.
- Inspect each source with the `grasp_get_table` tool before composing.
- Use UUID `table_id` component handles and the shared source `conversation_id`; do not use `table_slug` handles.
- If source tables are in different conversations, do not compose them in MCP V1. Explain that they need to be recreated under one setup conversation before composition.
- Explain the composition logic: union, intersection, dedupe, comparison, or enrichment base.
- Preserve source identity when it matters for auditability.
- After composition, inspect rows before filtering, screening, shortlisting, or ranking.
- Use `screening-and-shortlisting` when the composed universe must be qualified, prioritized, ranked, or narrowed to a shortlist.

## Recreate Existing Configs

Use when a user wants another table like an existing table.

- Inspect the existing table with the `grasp_get_table` tool.
- Identify entity type, activity, geography, source columns, workflow steps, filters, sort, layout, and name/description.
- Use the `grasp_create_table` tool when the source universe changes.
- Use the `grasp_update_table` tool when only view, enrichment, filters, or naming changes.
- Do not copy stale filters or field names into a new table until the new table columns have been inspected.
- Use the `grasp_read_docs` tool if the existing workflow includes raw source filters, data columns, or research payloads you are not certain about.

## Scope And Guardrails

- Before calling the `grasp_create_table` tool, choose the matching entity path because `business_activity` and `geography` are interpreted differently for company, buyer, and transaction tables.
- If a scoped search returns zero rows, report that result. Do not silently broaden the mandate.
- Use the `grasp_read_docs` tool for exact source, filter, data-column, or workflow payload details when uncertain.
- Use one table for a clear, coherent scope when it can express the user's mandate. Use separate or parallel `grasp_create_table` tool calls when the user asks for separate analyses or when docs, tool limits, or inspection show the combined search would be unsupported, capped, or misleading. Reuse the same `conversation_id` for separate tables created in one task, especially when they may later be composed.
