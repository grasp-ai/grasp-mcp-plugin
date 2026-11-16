---
name: sourcing-event-rosters
user-invocable: false
description: "Use when the user names or links to a conference, trade show, expo or other event and needs its roster (exhibitors, sponsors, speakers or attendees, the targets or buyers among them, or which of their companies are there), or wants their copy of an event's list checked or completed. Not for finding events, or which events companies attend."
---

# Sourcing Event Rosters

An event roster, the organisations exhibiting, sponsoring, speaking or attending at one edition, is a ready-made market map of active companies. The job: pin the right edition, have `grasp_collect_event_roster` collect its roster from the organizer and import it into a Grasp table, then work with that table. Narrowing and researching it afterwards is ordinary table work.

Call `grasp_setup_conversation` once and pass its `conversation_id` to `grasp_collect_event_roster`; the roster table is created in that conversation.

## Pin the edition

Pin down the event, edition (year) and location before collecting anything. Many events run in several cities or several times a year, and organizers keep old editions online. When the user names no edition, nothing in the request points to a past one, and the choice is between the next one, coming up within a few months, and one held months ago, collect the upcoming one without asking, and tell the user which edition you are collecting as you start. Otherwise, if the request fits more than one edition, or you can't tell which one the user means, ask before collecting. A quick web search for the event's dates is fine; leave the roster itself to the tool.

## Collect the roster

Call `grasp_collect_event_roster` once per edition with the event and the edition. Leave out `roles` unless the user asks to leave lists out ("just the speakers"), even when they name the lists they want: the worker then collects every public list of organisations, each row labelled with its role, so a request naming exhibitors, sponsors and speakers, or asking which investors attend or which companies are worth meeting, is answered from all of them and narrowed afterwards. Pass a link the user gave as `url`, and anything else they asked of the roster (extra fields, or a category, hall or country to keep) in their own words as `request`. Leave `request` out when they only named the lists they want: naming them there can narrow what the worker collects. Only one roster runs at a time in a conversation; for several events, wait for each job to finish before starting the next. Never collect a roster yourself with web search, page reading or code: the tool's worker does that and returns only its report.

The tool starts a job of up to 20 minutes. Poll it with `grasp_check_job_status` about once a minute. If you have to stop polling, tell the user the job carries on, and check the same job when they ask rather than starting a new one.

Repeating an earlier call in the same conversation returns that job's result, not a new collection. When the user asks to check an event again, for example for exhibitors added since, pass a new `idempotency_key`.

The finished job's `summary` is the worker's report, and `roster_status` says how it went. The report gives the rows against the organizer's count, website and country coverage, the pages it checked with the lists on each, lists it saw but could not reach, the page to cite and what is missing. In your answer, say which pages were checked and which lists were found, and name any list it could not reach and why, so the user knows what the table covers. Describe the roster by its counts, e.g. "1,950 exhibitors and 287 speakers", rather than calling it all, complete or full. Where you say where the roster came from, link the page the report names to cite; never cite a feed, API or file URL.

- **complete or partial:** the job also returns the table it imported. For partial, say what is missing and why, e.g. "1,950 of 1,956 listed exhibitors".
- **edition_unclear:** no table. Ask the user which of the editions the report found they mean, then call the tool again with that one.
- **not_found:** no table. Say what the organizer publishes and what the worker tried, and offer to work from a list the user has. Attendee lists are rarely public; most events keep them to registered participants, so never present exhibitors as attendees.
- **A failed job:** say what its message says. A worker that stopped without reporting imports nothing; a new call with a new `idempotency_key` collects again.

## The roster table

The import gives each organisation one row and matches it to a Grasp company, keeping the ones it can't match. Entries that name the same Grasp company, or match none and share a name, are merged, and their roles and other values are listed in one cell, separated by "; ". So the table has fewer rows than the roster. The report says how many roster rows went into how many table rows and how many matched.

Tell the user what that means for their list: matched rows carry Grasp data to filter and research on, while unmatched rows may be small private companies or missed matches, and can still be narrowed on the organizer's columns (category, hall, country). The `grasp_match` column says which rows matched, and they come first. Point out non-company entries, such as associations, media and public bodies, and say how many entries were merged and that a merged row lists every name the event used for it, so a wrong match shows up there. Ask before dropping non-company entries.

The table's columns keep the roster file's names, such as Company Name, Role, Website, Country and Booth. Take the exact column names for queries and research inputs from `grasp_get_table`.

If the worker skipped fetching websites from each exhibitor's page, offer a website research column on the same table. Explain that this adds websites to the existing list but does not re-match its rows to Grasp companies, and that it uses table research credits, so don't quote the worker's bulk-fetch estimate as its duration. On a yes, call `grasp_add_research_column` with `output_type` "extraction", `web_research` on, an "Organizer Website" output column, and the company name and any country and booth columns as `input_columns`. Name the event, the confirmed edition and the organizer's directory URL in the question, and ask for the company website linked from that exhibitor's organizer page, as the website URL only (e.g. `https://example.com`), or `Not found` when it cannot be verified. Use `where` to limit it to rows with no Website value (all rows if there is no Website column). If the column already exists, fill its missing cells with `grasp_run_table_outputs` instead. Don't collect the roster again just to add websites.

Once the roster table is in place, offer one follow-up, once: tag each organisation by type (investor, startup, corporate, public body, media, other) as a categorical research column on the table.

## When the user asks which of their companies are there

Their companies, in a table or a list, are the question; the roster table is the evidence. Collect the roster as above, then check each of their companies against the roster table with `grasp_query_table`. A company is "not listed" only if the roster covers the lists it could appear in; otherwise say which lists you checked. When they ask who is attending, exhibitors and sponsors are what you can check; say that visitors aren't listed.

A company can exhibit under another name than the one in their list. Look for every name it may use: the name they gave, the legal name in their table, its brands, and its parent or group. Match the website domain first, then the name with the country. Look at every candidate before counting it: a similar name in another country is usually a different company, and short names or abbreviations need whole-word matches. A match found only through a parent, group or sibling brand is unsure, with the name that matched, unless the organizer's entry names their company or uses its domain.

Answer in three groups: listed, with the name, booth and role the organizer shows; not listed; and unsure, with what didn't match. Say when you checked and whether the organizer still adds exhibitors, so "not listed" reads as "not on today's list".

If the organizer publishes no roster, or none the worker could read, say so and offer to research each of their companies instead: a categorical research column on their table with the categories "Confirmed", backed by the company's own announcement or an organizer listing, "No announcement found" and "Other", and report Other as unsure. A company that doesn't announce a show may still exhibit, so never present "No announcement found" as "not exhibiting".

## When the user brings the event's list

If they want it completed or checked (missing entries, booths, roles, websites or countries), collect the organizer's roster with the tool, then compare it with theirs, and add what theirs lacks; don't replace their rows or columns. Say which entries the organizer lists that theirs doesn't, and the reverse.
