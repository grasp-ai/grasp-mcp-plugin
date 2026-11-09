---
name: monitoring
user-invocable: false
description: "Use when the user asks about Grasp company monitors or monitored values: what is watched, what changed since a date, starting a recurring monitor on a table or one company, or stopping one."
---

# Monitoring

A monitor rechecks chosen columns of a table for its companies, or watches companies for events, daily or weekly. A changed column value replaces the old one in the table's cell, in every version with that column. Earlier values exist only in `monitoring`.

## What is watched

Use the `grasp_list_monitors` tool for each monitor's `monitor_id`, target, status, cadence, company count, signals, next run and app link. Its `watches` holds the exact column names and signals, with thresholds, in the same form as `grasp_create_monitor` takes them. Start here when the user asks what they monitor: the `monitoring` table has no status or cadence, and shows a monitor only once it has rows.

## What changed

Read the `monitoring` table with the `grasp_query_table` tool, the same way as any table but with no `table_id`:

```json
{
  "query": "SELECT source_name, company, column_name, value, reasoning, observed_at FROM \"monitoring\" WHERE kind = 'change' AND monitor_id IN ('<monitor_id>', '<monitor_id>') AND observed_at >= '2026-10-01' ORDER BY observed_at DESC",
  "usage_rationale": "See what these monitors found since 1 October."
}
```

Leave out the `monitor_id` filter to cover every monitor. Results are capped at 50 rows: when `truncated` is true, narrow the dates, count with `GROUP BY`, or page with `LIMIT` and `OFFSET`. Returned rows count toward the annual row allowance.

To join it to a table, pass that table's UUID as `table_id`, join on `entity_id` and filter the monitoring `table_id` column to the table's slug.

```json
{
  "table_id": "4f7b6d2a-8c31-4d91-9f2e-6a5c8b7e1234",
  "query": "SELECT t.company_name, m.column_name, m.value, m.observed_at FROM \"targets-a1b2c3\" t JOIN \"monitoring\" m ON m.entity_id = t.entity_id AND m.table_id = 'targets-a1b2c3' WHERE m.kind = 'change' AND m.is_latest",
  "usage_rationale": "Show each target's latest monitored change."
}
```

It holds every monitor the user can see: their chats' monitors, those of chats shared with their projects, their projects' saved-list monitors, and their own company monitors. One row per company and column each time it was added, got its first value, changed or was removed; checks that found nothing new have no row.

No rows does not mean nothing changed. Single-company monitors only record changes from November 2026, and saved-list monitors' older changes are also missing. A monitor that has found nothing yet has no rows either. Say this when the answer is empty.

| Column | Meaning |
|---|---|
| `monitor_id` | The monitor, as in `grasp_list_monitors` |
| `source_kind`, `source_name` | `chat_table`, `saved_list` or `company`, and its name |
| `table_id`, `table_version` | The table's slug, and the version the company was added from. Empty for a company monitor |
| `entity_id`, `company` | The row |
| `column_name` | The column, or an event signal |
| `role`, `input_for` | `selected` by the user, or `input` to the computed columns in `input_for` |
| `observed_at` | When, as ISO 8601 UTC text; compare it with ISO dates |
| `kind` | `added`, `baseline`, `change` or `deleted` |
| `value` | The value as text; cast numbers. NULL on `added` and `deleted` rows |
| `reasoning`, `sources` | Why, and the cited pages as JSON |
| `is_latest` | The newest row for its monitor, company and column |

## Start a monitor

The `grasp_create_monitor` tool starts recurring credit spend, so always get the user's OK first, in chat.

1. Find the target.
   - One company: `grasp_search_companies`, then pass the chosen `company_id`.
   - A table: `grasp_list_tables` or a table job's `table_id`, then `grasp_get_table` for exact column names and the row count. Monitors take the table's current version and up to 1,000 companies. Use `entity_ids` to monitor only some rows.
2. Choose what to watch.
   - `columns` (table only): AI research and computed columns to recheck. A computed column is recalculated from its research inputs, which are the columns checked and billed.
   - `signals`: company events. `acquired_by`, `acquisition`, `ceo_change`, `cfo_change`, `capital_raised`, `annual_accounts`, `linkedin_headcount_growth` and `linkedin_headcount_decline` (`threshold_percent` sets the year-over-year change, 30 by default), or `custom` with `custom_text`. A company monitor needs at least one.
3. For a table, use `grasp_list_monitors` to look for one of the user's monitors on the same `table_id` (`can_manage` true) whose `watches` has exactly the columns and signals you plan to pass. Compare column names, not display labels, ignoring case, and compare `threshold_percent` and `custom_text` too. The server joins only an exact match.
   - A match: tell the user the companies will join that monitor and keep its cadence, which may differ from the one they asked for. Stopping it later pauses the whole monitor.
   - No exact match, even a close one: tell the user this starts a new monitor. For companies in both monitors, anything both watch is checked, and billed, twice.
4. Tell the user the target, how many companies, what is watched and the cadence. Say that each check bills credits until the monitor is stopped, that a computed column rechecks its research inputs, and that filling empty cells of watched columns also bills credits. Do not work out a weekly number or quote a credit price. Wait for their OK.
5. Call `grasp_create_monitor`. Report the `cadence`, `weekly_checks` and `setup_checks` it returns and share its `app_url`.

The tool's result can differ from the request. A table monitor with the same columns and signals as an existing one adds the companies to it and keeps its cadence (`joined_existing_monitor`). A company that is already monitored is refused, with its `monitor_id`. A monitor that was stopped stays paused: send the user to the app to resume it. The tool also refuses when the plan has no monitors, the organization's allowance is used up, or the table is still being built or has an unfinished table job; a monitor can start once that job finishes.

Monitors on saved project lists need a Grasp project, so the user sets those up in the app.

## Watched columns

Editing or removing a watched column changes the monitor. Warn the user before `grasp_update_table` or a similar change touches one:

- An edited column starts again from its new cells.
- A column that is removed, renamed or can no longer be monitored stops being checked.
- A monitor left with nothing to check is paused, and so is one whose edit adds checks past the organization's allowance.

## Stop a monitor

The `grasp_stop_monitor` tool pauses a monitor by `monitor_id`: no more checks run or bill, and its history stays readable. If the user's words could match several monitors, ask which one. Anyone in a project can stop its saved-list monitors; only the owner can stop a table or company monitor.

Resuming, changing signals or cadence, and deleting happen in the app, so share the returned `app_url`. Never tell the user a monitor is deleted when it was paused.
