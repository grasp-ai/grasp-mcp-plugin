---
name: working-with-tables
user-invocable: false
description: Use when the user wants to inspect, search, filter, keep/remove/exclude rows, sort, query, describe coverage, read rows, export, or recover handles for an existing Grasp table.
---

# Working With Tables

Use table handles deliberately. Do not guess field names. Treat `grasp_get_table` previews as samples.

This is a table-mechanics skill. If the user wants to screen, qualify, shortlist, prioritize, rank, select top-N, map a competitor landscape, or turn a long list into a decision set, use the `screening-and-shortlisting` skill; call this skill only for the underlying inspection, coverage, query, filter, sort, export, or missing-row operations.

## Handles

- Use UUID `table_id` values as tool handles.
- Do not pass `table_slug` as a tool handle. Slugs are display, navigation, and SQL identifiers only.
- If the user refers to recent work, use the `grasp_list_tables` tool to recover the UUID `table_id` and, when needed for composition, `conversation_id`.
- If the user gives a recent async job, wait or check status to recover the UUID `table_id` and `conversation_id`.

## Workflow

1. Use the `grasp_get_table` tool for metadata, columns, version, row count, and links.
2. Use the `grasp_get_table_rows` tool for focused row reads, samples, top-N pulls, filters, and sorts.
3. Use the `grasp_describe_table` tool for missingness, top values, and distributions.
4. Use the `grasp_query_table` tool for read-only counts, aggregations, combined criteria, and computed comparisons. Results are capped, so export when the full result set matters.
5. Use the `grasp_export_table` tool when the user needs the full table or local analysis beyond query caps.

Use rows, describe, query, or export before making full-table, top-N, or coverage claims. When summarizing table/query results, state row counts and whether the result is a preview, sample, capped query, filtered row set, or full export.

## Filtering And Sorting

- Call the `grasp_get_table` tool before using a field name.
- Use the `grasp_describe_table` or `grasp_query_table` tool to check missingness, distributions, and categorical values before filtering.
- Do not filter on a field just because it exists in another table.
- Avoid keyword filters for semantic fit; add a narrow research column through the `table-enrichment` skill instead.
- Use the `grasp_get_table_rows` tool for simple filtered reads and top-N pulls.
- Use the `grasp_query_table` tool for counts, distributions, aggregations, and combined criteria.
- Use the `grasp_update_table` tool only when the user wants the filtered/sorted view persisted in Grasp, and include a short `version_summary`.
- If blanks remain after filtering, state that missing values were not fully tested.

## Missing Company In A Table

- Use the `company-lookup` skill first to validate the expected company and canonical URL/domain.
- Inspect whether the table scope, geography, source criteria, filters, size constraints, ownership constraints, and semantic research columns should include it.
- Check whether it is present under another name, URL, or parent before calling it absent.
- If it is outside scope, explain the mismatch.
- If it appears to be a false negative, present it as a corrected-table/manual-add/watch item and explain the evidence gap.

Use the `grasp_read_docs` tool for SQL, filters, exports, or data-column details when uncertain.
