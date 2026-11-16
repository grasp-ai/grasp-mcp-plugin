# Phase 0: the scoping session

The scope decides which players are on the map, so it is confirmed with the user before any search runs. A one-line ask is a starting point, not a scope: draw the grid, propose defaults for everything the user did not say, and confirm in chat. Before confirmation, look up only what the grid itself needs, such as a named company's profile with `grasp_get_company` or how the industry segments its own offerings. No universe search and no research columns.

## The scope grid

Two dimensions are always there: the offerings that make up the industry, named as the industry names them, and the geography as the user names it, a region such as the Nordics or DACH or a list of countries, with the countries it covers listed so searches and counts can run per country. Add a third dimension only where the industry's boundary actually runs along it, such as a customer segment, and say why. Mark every cell in, out or adjacent. An adjacent cell is named so later research cannot widen the scope into it, with the rule that decides a player straddling it: a player whose main business sits in an adjacent cell is out, however large its in-scope arm, unless that arm runs as a business of its own with its own brand and management, in which case the arm is the player.

Which value-chain stages are on the map is a scope decision of its own. Makers, distributors, installers and service firms of the same offerings are often separate archetypes of one map, but a purpose about one stage may leave the others out. Propose the stages from the purpose and state them in the grid.

## What the scope fixes

- **Size floor.** Players at or above it form the core universe; the rest are the long tail. Describe the split in words for this industry: a few players you expect among the main ones and how fragmented the rest is, with no size cut-off or player count, which the counts set, and no types of player, which the research finds. The number is set from the counts once the universe is built, as the floor whose core is deep enough to derive archetypes from and small enough to research every player properly, usually a few dozen to about 150 players. A user who wants a lighter or deeper map says so here. A floor the user names is used as it is.
- **Purpose.** The question the map answers for the deal, such as which players compete head on with a target's model, where consolidation is heading, or whether a new type of player is taking share. It decides which archetypes are worth separating and which axes are worth drawing. When the user gives none, infer one from the request, state it, and let the user correct it. State it as a question that names no types of player, since those come from the research.
- **Foreign players.** Whether players headquartered outside the geography but operating inside it are on the map. Recommend including them, since they compete for the same customers; the alternative is a map of the domestic players only.
- **Research attributes.** What is researched for every core player. The base set, each tailored to this industry before it is shown:
  - value chain position, as five to seven stages named for this industry, plus Other
  - value chain breadth, how many of those stages the player covers in-house: One stage, Two stages, or Three or more
  - key differentiator, the one dimension besides geography that best separates competitors here, with categories named for this industry, plus Other
  - geographic footprint
  - customer segment
  - active consolidator, whether the player is a buy-and-build platform
  - self-description, how the player describes what it does and for whom
  - named competitors, who the player names as its competitors
  - recent M&A, acquisitions and disposals in the last five years

  Show the categories of every categorical attribute, and invite the user to add attributes their deal cares about.
- **Types of player the user brings.** A set of types in the request is restated in the scope as the draft set, which is compared with what the research separates. Make sure the research attributes cover what separates those types.
- **Universe source.** Grasp builds the universe unless the user brings a company table, a Grasp table or a list to import with `grasp_import_table`; then check the scope against what they brought.

## Confirming

Present the grid in chat with the defaults filled in. The same message gives the plan in plain words, which the user needs before agreeing to a long run: a full map takes about 30 to 60 minutes, this message is the only time it waits for the user, researching the core players uses research credits where the organization's subscription charges for them, and once the scope is confirmed the user can leave it running as long as this chat stays open. That confirmation covers every later search and research column; say so plainly and don't ask again before each one. Ask at most three questions in the same message: the grid, with its stages, the split and the research attributes as drawn; the purpose; and the foreign-players question. Skip what the request already answered and restate it instead. Say in a few lines how the later choices will be made, so a user who would choose differently says so now: the floor set from the counts, leaders the industry names added to it, archetypes derived from the research and compared with the industry's own names, and the axis pair that separates them best. Invite the user to name any players that must be on the map; they join the core whatever their size. Put the confirmed scope at the top of every later deliverable.

A player found later to straddle two cells is placed by the adjacent-cell rule, and the closing message says so.

## Starting the page

Once the user confirms, call `grasp_write_competitive_map_page` before any search, with a new slug named for the map and the `header` alone: the title, the summary sentence, the currency, and "Set from the counts" as the floor until phase 1 sets it. If a call refuses at the limit, tell the user what the refusal says and stop: build no tables for the map.
