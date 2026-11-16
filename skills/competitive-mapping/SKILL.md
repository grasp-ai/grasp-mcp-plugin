---
name: competitive-mapping
user-invocable: false
description: "Use when the user explicitly asks for a competitive map of a market, or chooses one for a broad market request as sizing-markets describes: how an industry in a geography splits into archetypes of players, a two-axis chart of those players, and the trends that help or hurt each archetype, as the competitive mapping workstream of a commercial due diligence. Delivers archetypes derived from research, sourced trends per archetype, the consolidation deals and a key-player benchmark, on a page the user can open. A quick \"who are the main players\" question gets a normal answer, with this skill offered as a follow-up. A list of one company's competitors or peers is creating-tables, not a map. For how big the market is, use sizing-markets, which also says how to run both."
---

# Competitive mapping

A competitive map shows how a market splits into types of players: who competes how, which types are gaining, where consolidation is happening. Its substance is the archetypes, each a mutually exclusive type of player defined by what its members do and how they compete, and for each the trends that help or hurt its members. The chart places the key players on two axes chosen to separate the archetypes, and it is labelled "Illustrative" because it shows how the market differentiates rather than every player in it. The map answers a purpose: the question about the deal it exists for.

The map is of a market, never of one company. When the user names a target, the scope is the target's industry and geography, and the target is one player among the rest.

The universe and the core universe are Grasp tables, every per-player judgement is a research column on them, and `grasp_write_competitive_map_page` draws the page and returns a link to it. If `grasp_write_competitive_map_page` is not available, market research is not part of this organization's Grasp plan: say so instead of building a map another way.

When the table below says to read `skill:competitive-mapping/<part>`, call the `grasp_read_docs` tool with that as its `query` before doing anything else in the phase. Their rules bind as much as the ones here.

| Phase | What you do |
| ----- | ----------- |
| 0 | Read `skill:competitive-mapping/scoping`. Draw the scope grid, describe the split between main players and long tail, propose the purpose and the research attributes, and confirm in chat. Then start the page with its header. |
| 1 | Read `skill:competitive-mapping/universe`. Build the universe, collapse it into players, split it into the core universe and the long tail, and start reading industry sources. Set the floor and tell the user the counts. |
| 2 and 3 | Read `skill:competitive-mapping/archetypes`. Research the core universe, finish the industry sources, derive the archetypes, sort the core into them and draft two or three candidate axis pairs. Settle the set and the pair, then guess an archetype for the long tail. |
| 4 and 5 | Read `skill:competitive-mapping/output`. Choose the key players and place them on the axes, collect the trends, the deals, who owns the market and where the opportunities are, and build the page with `grasp_write_competitive_map_page`, part by part. |

Do not size the market: a market size belongs to `sizing-markets`.

## Rules that hold in every phase

- **Archetypes come from the data.** No archetype is proposed, named or hinted at before every attribute column on the core table has finished. Until then, name no type of player in any message, not in the purpose, the split between main players and long tail, or notes while searches run: name companies instead. What the industry calls its own player types is evidence to compare the set against, not a set to adopt. A set the user brings is the draft, not a reason to skip deriving one.
- **Archetypes are the finding; players and axes are placements.** A player added later is placed into the set, shown not to fit, or the reason for a new archetype. A change of axes redraws the chart and leaves the archetypes alone.
- **A player is counted once.** A group and its subsidiaries are one player unless the scope makes a business within the group the better unit; the universe phase says how to decide.
- **Every claim carries its source.** A leader named by the industry, a deal, a ranking and a trend each cite a page you fetched; a research column's answer supports a placement and is cited to its table row. A figure or claim seen only in a search result snippet is a lead: open the page it came from or leave it out.
- **Gaps are findings.** An archetype with no sourced trend, a key player whose position rests on its own description alone, an industry that publishes no ranking: each is stated on the page, never filled.
- **Grounded language.** A placement is your reasoned judgement with its rationale, and the page says so. An archetype is "consistent with" how the industry describes its players, never "validated" by it.

## Working rules

- **One pause, at scoping.** It is one chat message. Ask only what the request left open and what changes the map; a default the user is unlikely to overturn is stated, not asked. Put your recommended option first and mark it recommended. A question the user skips keeps its stated default. A user who says to go ahead confirms the scope as stated. A request that already says to go ahead or to take your defaults still gets the grid in chat before any search, so the scope is stated; the run then goes on without waiting.
- **After scoping, the choices are yours.** The user may be away until the page is done, so never stop to ask, and never end your turn to report progress: a status message that ends the turn leaves the map half built. Report in a line between calls, and end the turn only with the closing `skill:competitive-mapping/output` describes, once the page reply says every part is in. Make each choice by the rule its phase gives, and where none covers it, take the option a due-diligence reviewer would recommend from the evidence. Say each one in the chat as you make it, in a sentence with why. The only stops after scoping are the user saying stop and a failed tool call that names nothing to call next.
- **One phase at a time.** Each phase ends at a table the next phase reads. Do not research the core before the floor is set, and do not guess the long tail's archetypes before the set is final.
- **Tables.** Follow `creating-tables` and `table-enrichment` for the mechanics. Searches, compositions and research columns are jobs: wait for each with `grasp_check_job_status` before reading its rows, and read industry sources while one runs. A column's rows fill one at a time, so rows read while its job runs are partial; querying them is no way to wait. A table takes one research column per call, each after the last one's job has finished. Inspect and count with `grasp_query_table`, which returns at most 50 rows, so aggregate rather than list, and get the counts a step needs in one query. Research columns hold text, so compare them with their labels, never as booleans.
- **Reading the web.** Search and read pages with your own web tools, and cite each by its `url`. If you cannot read the web, say so in the scoping message: the map then rests on Grasp's tables and research columns, and the trends, rankings and deals no column finds are gaps on the page.
- **Currency.** Grasp's revenue columns hold USD, EUR and GBP. Use the one the user works in, or EUR when they work in another; ask at scoping only if you can't tell and the market spans several. Player sizes and the floor are stated in it.
