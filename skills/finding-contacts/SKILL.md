---
name: finding-contacts
user-invocable: false
description: "Use when the user explicitly asks for person-level outreach contacts for a selected set of Grasp companies or buyers: work emails, phone numbers, person LinkedIn profiles, CEO/CFO/founder/owner contact details, corporate development or M&A people, decision-makers, or outreach lists."
---

# Finding Contacts

Use contact columns or the dedicated contact lookup only after the company or buyer targets are clear. Both are outreach-grade workflows and are different from table fields, research columns, and one-company profile contacts.

Do not infer contact requests from ranking, shortlist, acquisition-target, PE add-on, or buyer-universe requests. Use this skill only when the user explicitly asks for people, emails, person LinkedIn profiles, outreach, or contact routes.

## Contact Source Choice

- People across a table, results to keep or export, or more than a few companies: add contact columns (see below).
- A quick lookup at a few selected companies, outside a table: use the `grasp_search_contacts` tool. Reserve it for selected domains where the user is likely to act on the result.
- Never use both for one request, and never use a research column to find people across a table.
- Reported/source fields: use the `table-enrichment` skill for company-level phone, website, generic email, or company LinkedIn fields when Grasp exposes them as table columns. These are not person-level outreach contacts.
- One-company profile contacts: use the `company-lookup` skill only for a one-company profile/deep-dive context. Do not use profile contacts as the default response to outreach requests.

## Guardrails

- Do not run `grasp_search_contacts` across a full long list. Use contact columns for a table.
- Contact research works on company and buyer tables, up to 200 companies per run. For a long list, suggest narrowing to the companies planned for outreach; if the user wants them all, keep running it.
- Pull domains from inspected table rows or validated company profiles. Do not guess domains when using `grasp_search_contacts` tool.
- Respect the `grasp_search_contacts` tool's domain, title, and result limits.
- Founder-owned, owner-managed, PE-backed, family-owned, or management-owned target criteria are ownership criteria, not contact requests. Use `finding-contacts` only when the user asks for people or outreach details.

## Contact Columns

Use the `grasp_update_table` tool with three steps under `workflow.steps.add`, even if the user asks for one column. All three share `brief`, `purpose`, and optional `roles`, and each call takes one such group:

```json
[
  {"type":"contact","config":{"output_column":"contact_1","strategy":"workflow","brief":"Find the owner or decision-maker to approach about acquiring a controlling stake in this company.","purpose":"buy_side","rank":1}},
  {"type":"contact","config":{"output_column":"contact_2","strategy":"workflow","brief":"Find the owner or decision-maker to approach about acquiring a controlling stake in this company.","purpose":"buy_side","rank":2}},
  {"type":"contact","config":{"output_column":"contact_3","strategy":"workflow","brief":"Find the owner or decision-maker to approach about acquiring a controlling stake in this company.","purpose":"buy_side","rank":3}}
]
```

- `brief`: the outreach goal, the decision wanted, and known context. For buyer teams, include the target's sector, geography, and scale.
- `purpose`: `general`, `sell_side`, `buy_side`, `financial_buyer`, `strategic_buyer`, or `expert`.
- `roles`: optional job titles in priority order. Without them, `purpose` picks the seat.

Adding the columns starts research, so do not follow with `grasp_run_table_outputs` for the first pass. Wait for the job and report its `contact_research_runs` counts accurately. Use `grasp_run_table_outputs` with all three columns only to fill pending rows or refresh when the user asks. Do not retry failures on your own.

Read cells with the `grasp_query_table` tool, for example `json_get_str(contact_1, 'title')`. A null cell is not researched yet; `found: false` means nobody suitable. Locked email, phone, and LinkedIn read as null, which does not mean research failed: `email_status`, `has_phone`, and `has_linkedin` say what is available. Present only returned people, titles, reasoning, and sources.

## Unlocking a Contact Column

Research leaves details locked and spends no unlock credits. To reveal them, use the `grasp_unlock_contact_emails` tool with `table_id` and `columns`, passing all requested columns in one list. It reveals email, phone, and LinkedIn together and writes them into the table.

1. Call it with `preview: true`. Nothing unlocks and nothing is spent. The result has `approval_token`.
2. Tell the user the `locked_count` and the price from the result and ask them to confirm, once for all columns. The original request is not confirmation. If they decline, stop.
3. Call it again with the same columns and that `approval_token`, without `preview`. Only the contacts the preview counted unlock, at no more than its price. It starts a job and returns `job_id`. If an unlock is already running on the table, it returns that job instead, and `unprocessed_columns` lists the requested columns it does not cover; unlock those after it finishes.
4. Poll the `grasp_check_job_status` tool until the job has finished. If the user wants to stop, the `grasp_cancel_job` tool stops it before its next page of contacts.
5. If the result has `time_limit_reached: true`, report progress briefly and call again with the same `approval_token`. This needs no new preview or question, and contacts already unlocked cost nothing.

The token lasts 24 hours. A newer preview of the same column replaces it, and a token not used yet can also lapse after a few previews of other columns. If the result says the token no longer applies, preview again and confirm the new count with the user.

Report the unlocks and `credits_spent` from the result, then read the revealed values from the table. If it has `stopped_early: true`, say so; a new preview shows what is still locked. Do not retry failures on your own. To unlock a subset, filter the table's saved view with the `grasp_update_table` tool first.

## Locked Emails and Unlocking in a Search

Contact emails from `grasp_search_contacts` may be locked (`email_locked: true` with `email: null`). Locked means the email exists but has not yet been revealed for the user.

- Never guess, fabricate, or substitute a locked email. Present the contact with the email marked as locked.
- To reveal locked emails, use the `grasp_unlock_contact_emails` tool with the contacts' `person_id` values. Contacts the user already unlocked are returned without re-fetching; contacts without a resolvable email come back as `not_found`.
- Only unlock contacts the user has explicitly selected, and confirm the selection with the user before calling. Never unlock an entire search result set unprompted.
- Re-running `grasp_search_contacts` returns real emails for contacts the user already unlocked (`email_unlocked: true`).

## Workflow

1. Identify the target companies or buyers.
   - From a table: use the `working-with-tables` skill to inspect rows and URL/domain columns.
   - From names: use the `company-lookup` skill to validate canonical domains.
2. Decide between contact columns and `grasp_search_contacts` (see Contact Source Choice). For contact columns, follow Contact Columns from here. The steps below are for `grasp_search_contacts`.
3. Translate the outreach goal into role filters.
   - For specific roles such as CEO, CFO, Founder, Owner, Partner, Head of M&A, or Corporate Development, pass explicit `titles`.
   - Use `senior_only` only as a broad seniority hint when the user asks for senior decision-makers without naming specific roles.
   - Use `seniorities` only when the user asks for seniority classes rather than titles.
4. Call the `grasp_search_contacts` tool with selected domains, explicit titles/seniority when useful, and a concise `usage_rationale`.
5. Present contacts with company/domain, person, title, available contact fields (marking locked emails), and missing coverage.
6. If the user selects contacts whose emails are locked, confirm the selection, then call `grasp_unlock_contact_emails` with those `person_id` values.

Never invent emails, LinkedIn URLs, source links, or people. Treat absent results as coverage gaps, not proof that no relevant contact exists.
