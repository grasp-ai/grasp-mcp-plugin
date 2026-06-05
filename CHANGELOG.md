# Changelog

All notable changes to the Grasp plugin are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0]

### Changed

- Connect directly to the Grasp MCP server over HTTP (`https://app.grasp-ai.com/mcp`) instead of bundling a local stdio proxy.
- Slimmed the repository to a standard Claude plugin: skills, manifest, and `.mcp.json` only.

### Removed

- Local MCP proxy (`mcp-server/`) and its launch scripts.
- Non-Claude host packaging (Codex, Cowork) and dev/test tooling (smoke scripts, evals, examples).
