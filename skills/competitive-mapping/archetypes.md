# Phases 2 and 3: core research and archetypes

This phase researches the core universe, derives the archetype set and the candidate axes from that research, settles both, then places every player of the universe into the set.

Every step below is part of the method, so none is skipped to save time; the sections after the list say how.

1. Compose the core table, one row per core player.
2. Add the nine attribute columns, one per call, each after the last one's job has finished: value chain position, key differentiator, geographic footprint, customer segment, value chain breadth, active consolidator, self-description, named competitors, recent M&A, and any the user added.
3. Fill the core table's ownership output; the page's owners come from it.
4. Derive the set from the finished columns, add `Archetype` and `Archetype consistent`, and look at every Other and inconsistent row.
5. Draft the axis pairs, then tell the user the set, how it compares with the industry's own names for its player types, and the pair with the runner-up, without stopping.
6. Add `Suggested archetype` on the universe table for the long tail and count it.
7. Read `skill:competitive-mapping/output` before choosing a key player or sending anything more to the page.

## Phase 2: the core universe table

Build a separate table with one row per core player, so the research runs on players rather than on legal entities. Compose it with `grasp_compose_table` from the universe table, its `filter_sql` keeping one row per core player: the group's own company where it is in the universe, otherwise the player's largest row. Players with no row to stand for them, such as a group company the searches missed, a named leader or a player the user added, go in a small table of their company URLs, found with `grasp_search_companies` and imported with `grasp_import_table`, composed into the core table as extra rows. Name the table for the scope. The universe table stays as it is; the long tail is counted there.

Add one research column per confirmed attribute with `grasp_add_research_column`, with web research, each asking about the player as a whole rather than the legal entity on the row:

- `categorical`, with the categories confirmed at scoping plus Other: value chain position, key differentiator, geographic footprint, customer segment
- `categorical`, with its three counts and no Other: value chain breadth
- `binary`: active consolidator
- `free-text`: self-description, named competitors, recent M&A
- whatever the user added, in the type its answer takes, with a yes-or-no attribute as `categorical` with "Yes", "No" and "Not found". Answer "No" when a source that would show it leaves it out, such as the player's own list of integrations

Once the last of them has finished, fill the core table's `ownership` output with `grasp_run_table_outputs`, since a table runs one job at a time; where `grasp_get_table` shows the table has none, add it first with `grasp_update_table` and `output_column: "ownership"`. It gives each player's owner type and ultimate owner for the page:

```json
{"table_id": "<core table>", "columns": ["ownership"]}
```

The scoping confirmation covers these columns, so add them one after another without asking. Finish the industry sources while they run. A player the sources name as a leader that is missing from the core is added to the core table and researched like the rest. Where a source ranks players or names their market positions, keep it; the key players and the page use it. The research columns support placements; a deal or a market position that reaches the page cites a source you fetched yourself, so check the ones the page will carry.

## Phase 3: deriving the archetypes

Start once every attribute column has finished on the whole core: the set comes from their answers, so one drawn before them is a guess. Count the core players by value chain position, breadth, key differentiator, footprint and size together, to see which combinations recur. Then read the self-descriptions and named competitors of the players in every combination, not only the largest players, since a small specialist is often an archetype of its own. Build as many archetypes as the research separates and the purpose needs, no more, plus Other for the few that fit none; a set too large to tell apart on one chart is merged until it can be. Each archetype is:

- **Defined by what players do and how they compete.** Each definition decides membership on its own, so every player fits exactly one archetype. A specialist archetype is defined by its focus, whatever its members' size; a scale archetype is defined by breadth or footprint and by size relative to the rest of this market. A definition names no revenue figure: a cut chosen for this run splits players the market sees as one type.
- **Steered by the purpose.** Separate the differences the purpose turns on and merge the ones it does not. Say which purpose this set serves.
- **Checked against the evidence.** Players that name each other as competitors usually share an archetype; a set that splits them needs a reason. Compare the set with the industry's own names for its player types and say where it follows them and where it departs, and why.
- **Named for what the members are and how they compete**, in a plain phrase for a partner's slide, such as integrated groups, decentralised networks or single-offering specialists. A source's passing nickname is never the name. Players whose whole business is the market's own product are its specialists and are named so. A name passes when an outsider understands it without the definition.

When the user brought a set of types, that set is the draft: sort the core into it below, keeping its definitions as they are, and list where the research separates players differently, such as a definition many members misfit or a group of misfits that share one model, in the open placements and the closing message.

Then sort the core into the draft set with a `categorical` `Archetype` column with `web_research: false`, reading the research columns and the size columns as its `input_columns`, with the definitions in its question and the set plus Other as its categories. Its reasoning is the one-line rationale per player. Add a `binary` `Archetype consistent` column with `web_research: false`: is the assigned archetype consistent with the player's actual activities and scale. Look at every Other and every inconsistent row before settling the set: a definition that misfiles several players is rewritten, unless the user brought it, and the column added again under a new name; a group of misfits that share a model is a candidate archetype.

## Candidate axes

Draft two or three axis pairs. Each axis is a dimension along which the archetypes sit apart, judged from what the research columns hold, and it need not run from worse to better. Note for each pair what it reveals for the purpose and which archetypes it separates or leaves overlapping. A pair that puts several archetypes in the same corner shows little: recommend the pair that leaves the fewest overlapping, and say how many share a corner. Size is a candidate when the purpose turns on scale, not by default.

## Settling the set and the axes

Tell the user in a few lines, without stopping: the archetypes with their core counts, how the set compares with the industry's own names, and the axis pair with the runner-up, so they can ask for a different set or pair once the page is done.

## Then

**Core.** Once the set is final, every core player has its archetype with a rationale, and the flagged rows have been looked at and either resolved or listed as open. An open item states the question left for the reader, not the reruns or hand corrections that led to it.

**Long tail.** The long tail gets a cheap pattern check, not research: the aim is to say where most of it sits, such as which archetype its many small firms belong to. Its players stay off the chart. On the universe table, add a `categorical` `Suggested archetype` column with `web_research: false` and its `where` limited to the long-tail rows, reading the description, products and size, with the final set plus "Not in industry" and Other as its categories. A research column fills up to 10,000 rows; fill the rest of a larger tail with `grasp_run_table_outputs`. Count the long tail per archetype and per country, leaving out "Not in industry" and counting rows that share an owner once. These counts are guesses, and the page says so.

Phase 4 starts once the core table holds every attribute column, the ownership output, `Archetype` and `Archetype consistent`, and the universe table holds `Suggested archetype`.
