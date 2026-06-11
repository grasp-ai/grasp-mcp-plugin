# Grasp Plugin

> Public source warning: everything in this directory is published to the public
> `grasp-ai/grasp-mcp-plugin` repository after merge to `main`. Do not include
> secrets, private endpoints, customer data, internal implementation details, or
> unreleased IP unless it is intentionally public.

A Claude plugin for Grasp-powered M&A and deal work. It connects Claude to the
hosted Grasp MCP server so agents can source companies, build and enrich tables,
screen and shortlist targets, find buyers, and pull contacts through natural
language — backed by Grasp's company, financial, ownership, and contact data.

## Features

- **Sourcing** — build company, buyer, and transaction universes from a business
  activity, geography, and size filters, or import a CSV/Excel list. Imported
  rows match best by URL/domain; organization number plus country and name plus
  country/location are also supported.
- **Screening & shortlisting** — turn long lists into qualified, ranked, top-N
  shortlists with coverage checks and transparent escalation.
- **Enrichment** — add financials, headcount, ownership, research, and computed
  columns to existing tables.
- **Company intelligence** — profile a single company (business, financials,
  ownership, employees, LinkedIn, deals, similar companies).
- **Contacts** — find person-level outreach contacts (CEO/owner/founder, corp
  dev, M&A) for selected companies or buyers.
- **Table work** — filter, sort, query (SQL), describe, and export tables.

## Prerequisites

- Claude Code CLI, Claude Desktop, or claude.ai.
- A Grasp account with an active subscription and MCP entitlement for your
  organization. Tool access is scoped to that entitlement.
- Network access to `https://app.grasp-ai.com/mcp`.

## Installation

Add this repository as a plugin marketplace, then install:

```text
/plugin marketplace add grasp-ai/grasp-mcp-plugin
/plugin install grasp@grasp-plugins
```

The plugin ships `defaultEnabled: false`, so enable it for your project via
`/plugin`, then run `/reload-plugins`.

It bundles a single MCP server (`grasp`) configured in `.mcp.json` to connect
over HTTP to the hosted Grasp endpoint:

```json
{
  "mcpServers": {
    "grasp": {
      "type": "http",
      "url": "https://app.grasp-ai.com/mcp",
      "alwaysLoad": true
    }
  }
}
```

## Authentication

You authenticate with your Grasp account on first connect. No API key is stored
in the plugin, and access is scoped to your organization's Grasp entitlements and
subscription.

## Usage Examples

See [`examples/`](./examples) for full walkthroughs. A few prompts to start:

These examples are intentionally lightweight so they can double as quick
workflow validation prompts during plugin updates.

```text
Build a list of Nordic pest control providers for Anticimex, 10–75 employees,
specializing in rodent control, then shortlist the 10 best targets.
```
```text
Find likely acquirers for a UK industrial MRO distributor doing ~£40M revenue.
```
```text
Pull the full Grasp profile for stripe.com and build a table of 15 similar companies.
```

## Permissions & data handling

- The `grasp` server exposes both **read** tools (lookup, list, query, describe,
  export) and **write** tools (create/update/import/compose tables, add research
  columns). Write tools modify your Grasp workspace.
- Requests carry only what you provide: company names, domains, filters, and the
  rows you explicitly pass to an import tool.
- The plugin does not read Claude chat history, memory, or uploaded files except
  rows you explicitly import. CSV/Excel files are parsed by the client/agent
  before calling the import tool.
- Data returned by Grasp is processed under the Grasp privacy policy:
  https://grasp-ai.com/privacy

## Notes & limitations

- This configuration connects to Grasp's hosted MCP server; there is no local
  server to install or run.
- Table creation, updates, and research run as **async jobs**; the skills poll
  job status automatically.
- Previews returned by read tools are samples — full-table, top-N, and coverage
  claims are backed by row, query, describe, or export results.

## Questions or issues?

Contact Grasp support at https://grasp-ai.com or open an issue in this
repository.
