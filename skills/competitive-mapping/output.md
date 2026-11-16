# Phases 4 and 5: key players, trends, deals and the page

The set is final and every core player has its archetype. This phase chooses the key players and places them on the chosen axes, then builds the page with `grasp_write_competitive_map_page` part by part as each piece is done. The work runs long, so the page fills in from the moment the positions do: a run that stops early still leaves a map.

Every step below is part of the method, so none is skipped to save time; the sections after the list say how.

1. Start the transaction search. Choose the key players and place them, then send `header`, `archetypes`, `map` and `key_players`, and `core` after them.
2. Search the web for each archetype's trends, then open the pages behind them with your web tools: a trend cites a page you opened, never a search result alone. Then send `trends`.
3. Open the announcement or report behind every deal that goes on the page and cite it, fill the add-on band's ownership, count the owners, then send `deals` with `consolidation`.
4. Choose the plays and their targets, then send `plays`.
5. Send `universe`, then `answer`, `sources` and `executive_summary`.
6. Close as Afterwards says.

## Key players and their positions

Choose the key players by how much they matter in this market, not by size alone: the ones the industry names as leaders, the brands its buyers know, the active consolidators, any player the purpose turns on, and the largest members where size itself sets them apart. A smaller player the industry treats as central goes on the chart before a larger one it does not. Put one to three per archetype on the chart so it stays readable; the page lists every core member under its archetype.

Place each key player on both axes yourself, from its research columns, its report and what the industry sources say, on a scale from 0 to 10 with a one-line reason per axis. A position that rests on the player's self-description alone is marked `self_described`, and a player whose position contradicts its archetype's is either misfiled or a sign the axes do not separate that archetype; resolve it or state it in `universe.open`.

For each key player take revenue with its year, EBIT margin, staff and owner from the table where it holds them, or from `grasp_get_company`. For a listed player, take them from its latest annual report or year-end release instead. A figure no source gives is null and shows as a dash, never estimated. `does` is one sentence on what the player does and how it competes, cited. `offering`, `segments`, `footprint` and `differentiator` are a short line each, in plain words, from its value chain position, customer segment, geographic footprint and key differentiator columns.

## Trends

A trend is a specific, sourced change that moves share or position between archetypes, whether it acts on one type of player or on the whole market: what is changing, how it helps or hurts each archetype it reaches, and the evidence behind it, fetched and cited. Which trends matter differs from market to market; the test is whether it changes which types of player gain. Read for each archetype in this order: the members' own annual reports, where the outlook, strategy and acquisition sections say what is moving; trade bodies and statistics offices for the quantity the archetype's business follows; regulation with its dates. Paid-report summaries come last and never carry a trend alone.

- Reasoning runs from evidence to a trend, never the other way. A trend you can state before you have found anything to cite is a hypothesis: it goes in the archetype's `gap`, naming what you looked for.
- A trend that could describe any market is not a trend. `claim` names it in a few words; `mechanism` states it in one or two plain sentences, fact first.
- `helps` and `hurts` list the archetypes the evidence says it moves. Leave an archetype out when the evidence says nothing about it.
- `impact` is how much it moves share between archetypes over the next five years: High where it changes which types of player gain, Medium where it shifts share at the margin, Low where it is real but small. The page lists High first.

## Deals

Gather the deals of the last five years from three places, starting when this phase does, since the searches run while the key players are placed:

- **Grasp's deals database.** Run one transaction search with `grasp_create_table` and `entity_type: "transaction"`, as `creating-tables` describes, for targets in the in-scope offerings and scope countries, in the map's conversation:

  ```json
  {"conversation_id": "<map's conversation>", "name": "<scope> acquisitions", "entity_type": "transaction", "business_activity": "<in-scope offerings>", "geography": {"countries": ["<scope countries>"]}, "source_filters": [{"column": "deal_year", "operator": ">=", "value": <five years ago>}]}
  ```

- **The research.** The industry sources and the core's recent M&A column.
- **Live search.** The database misses many deals, the small ones most. Run up to six web searches for acquisitions the two above do not show, across the archetypes and countries where the long tail is largest: the buyers' own announcements and trade press on bolt-ons.

A deal the database or a search result shows is a lead. It reaches the page once its announcement, the buyer's report or a trade-press article is fetched and cited, or once the recent M&A column on the buyer's row confirms it, cited to that row. One row per buyer and year, with the targets named in `what` and their number in `count`. `buyer_type` is the buyer's archetype id, or `pe` when a PE firm bought a core player itself; those firms also go in `pe_owners`. Give each buyer with deals a `strategy` in `consolidators`: two or three sentences on its model, where it buys, how often and its exit route, cited, linking to any play that names it. A deal by a platform held by a PE firm or a group goes under its owner's name, with the platform's archetype id as `buyer_type`, since the page groups the deals by owner.

Then write two to four `takeaways`, written once the plays below are chosen: the patterns a buyer should take from the deals and the ownership, such as where consolidation has happened and how, which buyers drive it and what they buy for, and where it can still happen. Each is one short sentence that leads with the conclusion and names the archetypes, buyers and countries rather than counting them, citing the sources of the deals behind it and linking to the row, buyer or year that shows it.

## Who owns the market

The `consolidation` part opens the M&A section on how fragmented the market is, by size band and by archetype, with a read on each.

- **Owners.** Each core player's `owner_type` and `owner` come from the core table's ownership output: independent is Independent, a PE or VC owner is PE, part of a larger group is Group, public is Listed, a foundation is Foundation, any other owner Other, and unresolved Not stated. The owner is the primary ultimate owner's name. Key players take the same.
- **The add-on band.** When this phase starts, fill the universe table's `ownership` output with `grasp_run_table_outputs`, with a `where` that picks the long-tail rows from a fifth of the floor up to it whose suggested archetype is in the industry. Where that is more than about 300 rows, raise the lower bound until it is about 300.
- **Counts.** `floor_m` is the floor as revenue in the header currency, in millions. With one `grasp_query_table` query on the universe table, count the long-tail players in each of the four bands and in each archetype, rows that share an owner once and "Not in industry" left out. Every band and every archetype with researched players carries them by owner type in its `owners`. The page adds the core players itself.
- **Reads.** Every band and every archetype gets one. A band is an Add-on pool where its players are too small to carry a platform, Platform candidates where independent players remain large enough to build on, and Owned where groups, PE and listed players already hold most of it. An archetype is Open where most of its players are independent and few deals touch it, Consolidating where buyers have been active in it in recent years, and Consolidated where a few owners hold most of its revenue.
- **Why and so what.** `why` is two to four points, each one fact with its number: the independent share, the deals by or into it, the revenue its largest owners hold, and why buyers consolidate it, from the deals' announcements and the trends. A point that restates the row's own counts needs no cite; every other point cites. `so_what` is the takeaway for a buyer in one sentence, then one sentence that links to the play, buyer or deal year it rests on.

## Where the opportunities are

The `plays` part ends the section on what a buyer could do: one play per archetype with independent players to buy, in the archetype's own terms.

- **The play.** A Complementary roll-up where the archetype's players sell different products to the same customers, so a platform buys products it can sell across its base. A Revenue consolidation where they sell similar products to similar customers, so the value is in one platform, one sales team and scale. Sell to the leaders where a few buyers already lead and keep buying, so the independents are worth most to them and there is no platform to build.
- **The platform.** For a roll-up or a consolidation, the core player of the archetype best placed to carry it: large enough to absorb the targets, and independent or held by an owner that is building, never a leader it would compete with. Name it as in `core.players`.
- **Targets.** With one `grasp_query_table` query on the universe table, list the add-on band's rows the ownership output found independent, with their suggested archetype, name, country, revenue, headcount, products and the first part of their description. Choose up to eight per play that fit it: different products for a roll-up, look-alikes for a consolidation, and for a sale the ones the leaders' recent deals resemble. `product` and `fit` come from the row's products and description; `domain` is the row's website domain.
- **The reasoning.** `thesis` is the takeaway: what to build or sell and where the value comes from, linking to the ownership row it rests on. `happening` is what is happening in the archetype, from the deals, the ownership and the trends, cited. `result` is what the play ends with, in a few words, and what that holds. `fit` is one line: for Sell to the leaders why no new platform is needed, otherwise how the platform and the targets fit together. `buyers` names up to four other buyers, or the likely ones when selling to the leaders, as named in the core or the deals.

Links tie the section together, so a reader can follow a conclusion to what it rests on. Write one as `[words](#id)`: `#own-band-1` to `#own-band-4` for the size bands, smallest first, `#own-<archetype id>`, `#play-<archetype id>`, `#buyer-<owner>` for a buyer as the page groups it, its name in lowercase with hyphens, and `#year-<year>`. Every takeaway, `so_what` and `thesis` links at least once, and a `strategy` links to the plays that name its buyer, never to the buyer's own panel; the reply names any link that opens nothing.

## The page

Call `grasp_write_competitive_map_page` with the slug the header was sent under, sending one or two parts per call: a call that carries everything is cut off before it reaches the tool. A new map after a change of scope takes a new slug, since the parts of the earlier map would carry over. Send `header` again with the floor, then `archetypes`, `map` and `key_players` as soon as the positions are in, then `core`, `trends`, `deals` with `consolidation`, `plays` on its own, and `universe` as each is done, and `answer`, `sources` and `executive_summary` last. Keep going until a reply says every part is in: a page without its trends, deals and plays does not answer the purpose. Only a failed call that names nothing to call next stops you short of that; then say which parts are still to come, and the user's next message continues. Fix every problem a reply names before the next call.

Plain words for a partner reading an investment memo:

- The page gives each section a plain heading. A section's `title` is the finding it opens with, in ten words or fewer, such as "Five groups hold 65% of core revenue", never a label.
- The `answer` points show under the map as the key movements: three or four findings, each one fact with its number. Give each point the `section` whose content backs it, such as `trends`, `deals` or `players`; the point then links there, and the sources are cited in that section rather than on the point.
- A lede is one sentence that adds to its title: the number behind it, a contrast or the exception, never method. How the data was built, how the archetypes were chosen, what a figure includes and how positions were set go in `universe.method`; the page already labels the map illustrative and the positions as judgement.
- A figure that has a field goes in it rather than in a sentence; where a title, lede or point does state a share, write it as a number.
- A player's `name` is the one the industry uses, without legal suffixes or "Group", so the chart can label it, and the same in `core.players` and `key_players`, so the players table lists it once. Where it differs from the company name on the player's core table row, give the row's company name exactly as `row` in `core.players`, so the player opens its row.
- A definition is one line that decides membership; an `outlook_reason` is one line.
- Join clauses with commas, colons or full stops, never em-dashes or middle dots.

`header.summary` is one short sentence under the title on the industry, the geography and what is in, naming at most the one or two exclusions a reader would otherwise assume are in, such as "Commercial HVAC installers and service firms in the Nordics, excluding manufacturers and wholesalers." The full scope stays in the chat. Revenue is in millions of the map's currency, and `header.currency` says which. `core.players` holds every core player with its archetype, owner type and owner, and the scope countries it works in where that is more than its home country, so the page computes shares by archetype and by owner from it and lists the largest beside the key players. The owner comes from the table, as for the key players, and is left out for a player that is listed itself. `core.below_floor` says where most of the long tail sits, with two or three example players. `universe` holds the universe counts per country, with the open placements; `universe.method` is two to four short lines. Open placements and method lines tell the reader what is still open, such as a borderline player or one left out of scope, or how the map was built, never the run's own corrections.

Sources are numbered by their position in `sources`. A web page or a PDF you read is its `url`, and the core table is its `table_id`, cited with the company name on the row; it is the only table in `sources`. A report a Grasp tool reply names by its file path is that `file_path` exactly as printed, with its `url` beside it where the reply gives one, so it also opens outside Grasp. Cite with `cites` where a field has one, and inline in ledes and in an answer point no section backs as `{{n}}` or `{{n:page}}`.

## Afterwards

Close with the answer, then the choices you made after scoping that shape the map, each in one line with why:

1. the floor and the core it gave, in players
2. how the archetypes compare with the industry's own names or the user's set
3. the axis pair with the runner-up
4. any player placed by a cell's rule.

Then give the link the last call returned, as it is; it opens the page in the map's Grasp chat once the user signs in, and a colleague needs access to that chat to open it. End with an invitation to change the map in this chat. Swapping the axes, moving a player, renaming or merging archetypes, and adding or removing key players resend the part they change and need no new research beyond placing a player on a new axis. A company the user adds is researched on the core table like the rest first; then it is placed in its archetype, shown not to fit the scope, or, when it fits none, put to the user as the reason for a new archetype.
