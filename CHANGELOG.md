# Changelog

All notable changes to the Grasp plugin are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.2.0]

### Added

- `grasp_search_companies` resolves a free-text name/domain query and/or an exact `org_number` (optional `countries` filter) into ranked candidate legal entities with `company_id`. It is a lexical identity search, not a company-list builder; lists and universes stay with `grasp_create_table`.
- `grasp_get_buyer_deals` fetches the complete deal history of one known buyer by `company_id`: paginated deal records (newest first) with target identity and financials, deal year/status/stake, deal size, enterprise value, implied equity value, and EV/Sales, EV/EBITDA, deal-size/Sales, and deal-size/EBITDA multiples with derivation types, plus a coverage summary. Deals carry no stable ids yet.

### Changed

- **Breaking**: `grasp_get_company` now accepts only a `company_id` (from `grasp_search_companies` or a table company-id column). The previous URL, name, and org-number lookup modes are removed; identity resolution is the search tool's job. Old-style calls fail schema validation with an error pointing at search.
- The `deals` section of the company profile now returns an acquisition-history summary (total deal count, latest deal year) and routes per-deal detail to `grasp_get_buyer_deals`.
- Deal-data routing in the skills: known-buyer deal history goes through `grasp_get_buyer_deals` (lookup, full recall); transaction tables are reserved for semantic transaction discovery by target type.
- The `company-lookup` skill teaches the new flow: search candidates, pick the correct legal entity, then fetch by `company_id`. Company ids are session-scoped and should be resolved fresh rather than cached.

## [1.1.5]

### Added

- New `grasp_setup_conversation` tool. Call it once per table-building scope and reuse the returned `conversation_id` for every table created in that scope, so related tables land in one Grasp conversation and stay composable.

### Changed

- **Breaking:** `grasp_create_table`, `grasp_import_table`, and `grasp_compose_table` now require `conversation_id`. Calls without it are rejected.
- **Breaking:** table tools now require UUID `table_id` handles. Slugs are display, navigation, and SQL identifiers only and are rejected as tool handles.
- `grasp_compose_table` source tables must belong to the supplied `conversation_id` and have finished building. Tables from different conversations must be recreated under one setup conversation before composing.
- Status, list, and get tools now return `conversation_id` so existing table handles can be recovered.

## [1.1.1]

### Changed

- Updated repository references and install instructions to `grasp-ai/grasp-mcp-plugin`.

## [1.1.0]

### Changed

- Connect directly to the Grasp MCP server over HTTP (`https://app.grasp-ai.com/mcp`) instead of bundling a local stdio proxy.
- Slimmed the repository to a standard Claude plugin: skills, manifest, and `.mcp.json` only.

### Removed

- Local MCP proxy (`mcp-server/`) and its launch scripts.
- Non-Claude host packaging (Codex, Cowork) and dev/test tooling (smoke scripts, evals, examples).
