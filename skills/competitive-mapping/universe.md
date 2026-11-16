# Phase 1: universe, players, core and long tail

The universe is every player found inside the scope. This phase builds it, collapses companies into players, and splits the players into the core universe, which is researched in depth, and the long tail, which is only counted. Nothing about what players do is researched here beyond what the split needs.

Every step below is part of the method, so none is skipped to save time; the sections after the list say how.

1. Run the searches, add the four data columns to each (filed and estimated revenue in the map's currency, USD, EUR or GBP, the revenue year and the global ultimate owner), and compose the universe.
2. Start reading the industry sources while they run.
3. Size every row, filed revenue first, then estimated revenue, then headcount, and pick three candidate floors.
4. Add the `Player` column and group the rows into players by owner.
5. Check the core candidates are in the industry.
6. Set the floor and tell the user, in players rather than rows, the universe, the core and the long tail, with the core at the other candidate floors.

## Building the universe

Follow `creating-tables` for the mechanics. Run one `grasp_create_table` search per in-scope offering and value-chain stage, with the foreign-players answer as `geography.include_multinational_companies`, wait for each with `grasp_check_job_status`, and combine them into one universe table with `grasp_compose_table`, all in the map's `conversation_id`. The scoping answers settle every alignment question for each search, filter and research column in the run; do not ask again. A stage where many local firms compete, such as installation or service, is searched country by country; a stage held by a few players selling across borders, such as making or distributing, is searched across the whole geography. A search returns at most about 10,000 rows. A search that hits that cap is split into the industry's own sub-trades; one that still hits it is a sample, and the page's method lines say so.

A sizing company table already in this conversation is one component of the universe: compose it with searches for the stages and countries it leaves out. The data columns below go on those searches and every research column on the composed table, so the sizing's table stays as its own tools left it.

On every search, or on a table the user brought, add with `grasp_update_table` and `workflow.data_columns.add` the data columns the split needs: `revenue_last_available_year_<ccy>` and `estimated_revenue_<ccy>` in the map's currency, `usd`, `eur` or `gbp`, `year_of_revenue_last_available` and `global_ultimate_owner`; headcount is already on a company table. Add them on each search rather than after composing; a composed table carries its components' data columns. For a map in EUR:

```json
{"table_id": "<search table>", "workflow": {"data_columns": {"add": ["revenue_last_available_year_eur", "estimated_revenue_eur", "year_of_revenue_last_available", "global_ultimate_owner"]}}}
```

Never set `workflow.view.filters` on the universe table: the rows it would hide are the long tail. The owner column is Grasp's ownership data for the rest of the run: check it before calling a player's ownership unknown or a source gap.

## Industry sources

While the first searches run, start reading how the industry describes itself, and keep reading through the next phase: rankings and member lists of the trade bodies, industry reports, trade press and the annual reports of the largest players. Note three things, each with the page it came from:

- players the industry names as leaders, major players or notable entrants in this scope
- the industry's own names for its types of player
- acquisitions, mergers and buy-and-build platforms in the last five years

Key-player lists in top-down market reports vary in quality: use them only to check that no leader is missing; a player that only such a list names joins the core only when its own site or filings show it is in scope and of a leader's size.

## Size

A row's size is its filed revenue in the latest year; Grasp's estimated revenue where it files none; and headcount only where neither exists, with the floor's headcount equivalent stated with the counts. A revenue per employee above EUR 5M is a holding company's or a group's figure on a subsidiary row, and that row is sized by headcount. A group headquartered outside the geography is sized by its rows inside it, never by its global revenue, and the counts say so.

Once rows are sized, pick three candidate floors at round figures from the spread of sizes, the lowest giving a core of no more than about 200 players. Until the floor is set, the in-industry check below runs on every player that reaches the lowest candidate.

## Players

A player is a group, or a business within a group, chosen so no business is counted twice. Group the rows by their global ultimate owner:

- An owner that is an operating group in this industry makes its rows one player, named for the group.
- An owner that is an investment firm, a family office or a holding company outside the industry is not a player; each operating group beneath it is.
- A group whose business in one scope country runs as a business of its own, with its own brand and management, can be that business when the scope is that country alone. Across a wider scope, it is the group.

Make these calls for every row that could reach the core: rows at or above the lowest candidate floor, and every row of an owner whose rows together reach it. Add a `Player` research column with `grasp_add_research_column`, its `where` limited to those rows. Where the second or third rule above applies, it answers with the operating group's or business's name as Grasp registers it, and otherwise with "Owner". A row's player is then its `Player` answer, or, where that is "Owner", the global ultimate owner's name as the owner column holds it, so no name is retyped. Before counting, check the `Player` answers for one player spelled two ways, a name that matches no company Grasp holds, and rows under one owner given different players without a reason.

## Core universe and long tail

The core universe is every player at or above the floor, every smaller player the industry sources name as a leader or a notable entrant, and any player the user adds. The long tail is every other row: a row whose player is in the core is never in the long tail, whatever its own size. Rows that share an owner count as one long-tail player, and a row with no owner counts as its own.

Before counting, check the core candidates are in the industry with one research column, `output_type: "binary"`, its `where` limited to their rows, applying the confirmed grid: is the player's main business in the in-scope offerings and stages, by the rule written on each adjacent cell. A player whose main business sits in an adjacent or out cell is out of the core, however large its group; so is a supplier of generic inputs or a customer. They stay out, since the confirmed grid puts them out; name the largest of them in the open placements so a reader can ask for them back. Where that leaves a core too thin to derive archetypes from, about a dozen players or fewer, add back the dropped players that compete head-on with the core, the ones core players or the industry sources name as competitors. A player added back, or kept at the user's request, is sized by its in-scope business rather than its group's revenue, and the page marks it as an adjacent competitor. The long tail's relevance is judged later, by the cheap archetype guess.

## Setting the floor

Take the candidate floor whose core is deep enough to derive archetypes from and small enough to research every player properly, usually a few dozen to about 150 players, or nearest the depth the user asked for at scoping. A floor the user named at scoping is used instead, with its core count stated. Then tell the user in a few lines, without stopping: per country and in total the rows found, the players they collapse into, the core universe and the long tail, the core candidates dropped as not in the industry, the floor chosen with the core count at the other candidates, and which searches were samples. The same counts go in the page's universe part.
