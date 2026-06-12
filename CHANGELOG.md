# Changelog

All notable changes to the Grasp plugin are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
