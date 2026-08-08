---
title: OKF v0.2 Quietly Admits the Folder Has a Ceiling. The Way Up Is a Library.
url: https://medium.com/@davidroliver/okf-v0-2-quietly-admits-the-folder-has-a-ceiling-the-way-up-is-a-library-25fa54e872f9
description: Six weeks after shipping the Open Knowledge Format, Google has shipped v0.2. The commentary has focused on trust and provenance. The more interesting story is structural: the new fields only earn their keep if you can query them across a corpus, and a folder of Markdown files cannot do that past a certain size. There is a threshold; the index is how you cross it, and the graph database is where the road leads, especially once your knowledge stops being text. But the destination isn’t a bigger corpus. It’s a federated one.
---

Two weeks ago I wrote that when I opened Google’s Open Knowledge Format, my vault of Markdown files had already exceeded it, so I supersetted rather than adopted. On 25 July, six weeks after v0.1, Google shipped v0.2. The speed alone is worth noting: most draft specs never get a second version, let alone one inside two months.

The headline changes are about accountability. v0.2 adds four families of optional frontmatter: provenance (sources), trust (generated and verified), lifecycle (status and stale_after), and an attested-computation mechanism for proving an agent ran the query it was supposed to. The reasoning is sound. When a human writes a document, accountability is implicit because a name is attached. When an agent writes ten thousand concepts overnight, whatever reads them next needs signals it can actually inspect. The verified field, with its human: actor prefix, gives you three tiers of confidence (unverified, machine-confirmed, human-reviewed), arriving at precisely the moment the web fills with text nobody has read. Suganthan Mohanadasan's write-up of rebuilding his own bundle on v0.2 is worth your time; his admission that only 18 of his 61 concepts could defensibly claim human verification is the most useful field report on the spec so far.

So the provenance reading is correct. It is also incomplete. Look at what these fields are for and a second story emerges, one the spec doesn’t tell but can no longer avoid.

Metadata you can’t query is decoration
Consider the question v0.2 exists to answer: which of my concepts are past their stale_after date and have never been verified by a human?

That is a corpus-level query. It touches every file, filters on two frontmatter fields, and joins the answer against a review workflow. On a bundle of forty concepts, an agent can brute-force it: open everything, parse everything, answer. On four thousand concepts it’s an expensive scan you’ll want cached. On forty thousand, across text, diagrams, and recorded walkthroughs, it is a database query wearing a YAML costume.

This is the quiet tension in v0.2. Version 0.1 defined a format an agent could read. Version 0.2 adds fields an agent needs to query, and the spec explicitly lists storage, serving, and query infrastructure as non-goals. Google has shipped the schema for a knowledge database while declining to mention the database. I don’t say that as criticism. Declaring query infrastructure out of scope is the correct call for a portable format. But it means every serious adopter will hit the same wall, and the spec already contains the evidence.

The spec has been confessing all along
Three details in the specification give the game away.

The first is index.md. The spec describes it as supporting "progressive disclosure": letting an agent see what's available before opening individual documents. Read that phrase again. It is an admission that walking the tree stops working at some size, made in a v0.1 document. An index that exists so a reader can avoid reading everything is the seed of every retrieval system ever built. The spec even permits consumers to synthesise an index when none is present, which concedes the point twice: the index is so necessary that machines should generate one if the author forgot.

The second is the link model. Links between concepts are standard Markdown links, and the spec is explicit that the kind of relationship (depends-on, joins-with, supersedes) lives in the surrounding prose. Consumers building a graph view “typically treat all links as directed edges of an untyped relationship.” That is fine when a language model reads one document with full context. It is useless when you want to ask a structural question: what breaks downstream if I deprecate this concept? Untyped edges can tell you something is connected. Only typed edges can tell you how, and typed, queryable edges are the defining feature of a property graph.

The third is log.md: an append-only, date-grouped record of changes. An optional event log, sitting next to the data it describes. Anyone who has built an evented system will recognise the shape.

An index for retrieval, edges for relationships, a log for history. The folder isn’t an alternative to a database. It is the larval form of one.

The threshold, from experience
I can tell you roughly where the wall is, because I’ve hit it.

My own vault crossed it somewhere in the low thousands of documents. Grep still worked, in the sense that it returned results; it had stopped working in the sense that mattered, because an agent burning its context window on directory listings and false-positive matches is an agent with less capacity for the actual task. The fix was a BM25 index over the whole repository, and it’s simple enough that I’ll show you the whole design here.

Building the index
The entire thing rests on a decision most people skip past: SQLite’s FTS5 extension has BM25 ranking built in. That means no search server, no daemon, no Python ranking library recomputing scores over the corpus on every run. The index is one portable .db file, built with the standard library, and (this is the part I care about) a human can query it directly with the sqlite3 CLI while an agent queries the same file through an MCP tool. One index, two audiences.


Build and serve: the repo is chunked by heading into one FTS5 file, rebuilt from scratch on every commit, and read two ways: the sqlite3 CLI for humans, an MCP tool for the agent.

The schema is three columns:

CREATE VIRTUAL TABLE chunks USING fts5(
    path UNINDEXED,   -- file location: retrievable, never searchable
    heading,          -- breadcrumb, e.g. "Governance > Intake Stage"
    content,
    tokenize = 'porter unicode61'
);
Two of those lines carry design weight. Marking path as UNINDEXED means a query for orders.md matches concepts about orders rather than filenames containing the string, and the porter tokeniser means governance matches governing without anyone maintaining a synonym list.

The build step walks the tree and splits every file by heading section before inserting. This is the step that determines whether the index is any good. Index whole files and a sprawling three-thousand-word ADR will outrank a focused two-paragraph answer on sheer term frequency; chunk by heading and each section competes on relevance alone, and the heading column preserves a breadcrumb telling the agent where in the file the answer lives. Strip the frontmatter delimiters but keep fields like title and tags in the indexed text: they're high-signal vocabulary.

Querying is one statement:

SELECT path, heading,
       bm25(chunks, 0.0, 8.0, 1.0) AS score,
       snippet(chunks, 2, '»', '«', ' … ', 8) AS extract
FROM chunks
WHERE chunks MATCH ?
ORDER BY score
LIMIT 10;
The weight vector (0.0, 8.0, 1.0) boosts heading matches eight-to-one over body matches, on the theory that an author who put your search term in a heading was answering your question on purpose. FTS5's snippet() hands back a highlighted extract, which is exactly what an agent wants in its context window instead of the whole file.

The agent-facing layer is then almost embarrassingly thin: a FastMCP server of roughly thirty lines exposing one tool, search_docs(query, top_k), that runs the query above and returns {path, heading, score, snippet} dicts. Register it with your agent runtime and retrieval goes from "walk and hope" to ranked results in milliseconds. The final piece is a rebuild hook: a git post-commit hook or CI step that drops and recreates the index from scratch on every change. Rebuilding my entire vault takes seconds; there is no incremental-update logic to get wrong.

That rebuild-from-scratch property is the important part, and it’s why none of this argues against OKF. The index is derived. The Markdown remains the source of truth: human-readable, diffable, portable, everything the format promises. The index is a disposable acceleration structure; lose it, and you’ve lost nothing. This is the pattern the whole evolution follows: the folder stays canonical, and increasingly capable views get generated from it.

Which gives you a ladder, with each rung forced by scale rather than fashion:

A folder, which an agent walks directly. Sufficient for the hundreds of concepts, and where everyone should start.

A curated index, OKF’s index.md, which trades authoring effort for cheaper discovery. Sufficient until the index itself is too long to curate honestly.

A generated search index, lexical, then hybrid lexical-and-vector, rebuilt from the files on every change. Sufficient until your questions stop being about finding documents and start being about relationships between them.

A property graph, with concepts as nodes, typed edges, and frontmatter as queryable properties. At this point stale_after audits, blast-radius analysis, and multi-hop retrieval become one-line queries. In my day job, the equivalent layer runs on a graph database feeding GraphRAG retrieval, and the accuracy gap over flat vector search on relationship-shaped questions is not subtle.


The scaling ladder. The thresholds are calibration points, not laws: the behavioural trigger (an agent wasting context on directory listings) outranks the numeric one.

Every rung derives from the one below. You never abandon the folder; you stop asking it to do jobs it was never shaped for.

Multimedia collapses the ladder
Here is the part that turns a gentle evolution into a forced march. OKF concepts are UTF-8 Markdown. Images, audio, video, and diagrams enter the format only as URIs in a resource field or links in the body: the knowledge about the asset is in the bundle, but the asset itself is opaque to every mechanism the format offers.

That holds up while your knowledge is mostly prose. It fails the moment it isn’t. You cannot grep a video. You cannot diff an architecture diagram in any way a human can review. A curated index.md can list a recorded incident review, but the description will always be a lossy shadow of the content. The only retrieval primitive that works uniformly across text, image, and audio is the embedding, and the only structure that can hold "this diagram depicts that system, which this runbook remediates, which that recording discusses" is a typed graph. A multimedia knowledge base doesn't climb the ladder rung by rung; it needs the vector index and the graph on day one, because the folder-native tools (grep, diff, walking, reading) never worked on the content in the first place.

Enterprise knowledge is heading exactly this way. Meeting recordings, whiteboard photos, dashboard screenshots, and agent-generated diagrams are becoming knowledge artefacts as legitimate as any wiki page. A format that treats them as external URIs has drawn its boundary at the edge of yesterday’s corpus.

Make your metamodel graph-ready now
If the graph is where the road leads, the sensible question is what to put in your frontmatter today so the eventual migration is a transform rather than an archaeology project. OKF’s extension mechanism makes this legitimate: producers may add any keys they like, and conformant consumers must preserve them. Everything below is spec-compatible on the day you add it.

Give every concept a stable identity. OKF’s concept ID is the file path, which means moving a file changes what the concept is. Graphs need durable node keys. Add an id (a slug or UUID that never changes) and an aliases list that accumulates old paths on every rename. When you eventually load the graph, id becomes the node key and aliases become the redirect table that stops history breaking.

Promote your important relationships out of prose. This is the single highest-value change. The spec leaves relationship kinds in the surrounding sentences, which a graph loader cannot recover reliably. Add a links block that states them outright:

id: proc-model-intake
links:
  - rel: part_of
    to: governance/lifecycle
  - rel: depends_on
    to: platform/model-registry
  - rel: supersedes
    to: archive/intake-v1
Keep the prose links for human readers: the frontmatter block is the machine’s edge list, and each entry is a typed edge waiting to be loaded. Hold the rel vocabulary to a controlled handful (part_of, depends_on, supersedes, describes, remediates, discusses cover a remarkable amount of ground). Every predicate you invent casually now is a reconciliation job later.

Treat type as a future node label. The spec deliberately declines to fix a taxonomy, which is right for interoperability and wrong for you. BigQuery Table, bigquery-table, and Table (BigQuery) are one label in your head and three in a database. Write your type vocabulary down (a dozen entries is usually enough) and lint against it.

Keep property values scalar and typed. Dates as ISO dates, statuses from an enum, booleans as booleans. v0.2’s own fields model this well (stale_after is an absolute date precisely so staleness is a fact rather than a calculation) and every field you keep disciplined now is a node property you can range-query later rather than a string you parse apologetically.

Give binary assets an identity too. For every image, recording, or diagram a concept references, record a media entry with the URI, a content hash, and eventually a pointer to its embedding. The hash makes the asset a stable, deduplicable node even when the file moves hosts; the embedding pointer is the hook the multimedia retrieval layer will hang off.

Do these five things and the migration, when it comes, is a hundred-line loader: parse frontmatter, emit nodes keyed on id with type as label, emit typed edges from links, attach properties. Skip them and the same migration is a scraping project against your own prose. The metamodel is the cheap part of the graph: you can carry it in YAML for years before you need the database that exploits it.

The destination isn’t a bigger corpus
Everything so far describes one body of knowledge getting larger. That’s the easy half. The interesting half starts the moment a second team wants in, and it’s where the ladder stops being about scale and starts being about people.

The instinct at that point is to centralise. One repository, one taxonomy, one review queue, one team accountable for everybody’s knowledge. It fails the way it always fails: the cost of contributing rises until only the people paid to contribute do, and a knowledge base nobody volunteers to feed is already dead: it just hasn’t been decommissioned yet.

Federate instead. Every team keeps its own bundle: its repo, its review rituals, its release cadence, its owners. A library process syncs the bundles and builds the derived views over the union (one search index across all of them, eventually one graph across all of them) and never edits a team’s files. A wrong concept routes to the team that owns it, exactly as a wrong function would.


Federate, don’t centralise. The library owns the derived views; every bundle keeps its owner.

What makes this work is far smaller than it looks. Not a platform. Not a schema board. Two JSON files: the type vocabulary and the relationship vocabulary from the previous section, versioned in a small repo of their own. A dozen types and six relationship names is very nearly the entire cross-team agreement, and it’s the kind of agreement that survives a reorganisation, because it describes the shape of knowledge rather than the shape of the org chart. Agree on little. Interoperate on everything.

Three details separate a library from a pile.

Links across bundles have to be qualified. Inside a bundle, to: table-orders resolves against IDs you control. Across bundles it becomes to: data-eng/table-orders, where the bundle name is the namespace, so it needs to be stable and unique, like every other identifier here. This is also, incidentally, why wikilinks don't survive federation: they resolve by filename, and filenames collide the instant two teams both write a note called customers. If your authors want wikilinks in the editor, compile them to paths at build time. What gets published carries paths only.

Trust has to mean the same thing everywhere. This is where v0.2’s headline feature quietly earns its keep, and it isn’t within a bundle: it’s across them. When generated and verified carry the same three tiers in every bundle, an agent reading across five teams can prefer a human-reviewed concept over an unverified one while knowing nothing whatsoever about those five teams. Shared provenance semantics are what let you use knowledge you didn't produce, which is the only kind a library contains.

Verification has to be a side effect of something you already do. Suganthan’s 18-of-61 isn’t a discipline failure; it’s what you get when verifying is a separate chore competing with real work. Wire it to an existing ritual instead: make pull-request review the verification event, so the reviewer’s approval is what writes the verified entry. Then nobody has to remember to verify anything, and the stamp means something, because a second person genuinely looked.

And the question this article opened with (which of my concepts are past their stale_after date and have never been verified by a human?) stops being a thought experiment. Across a federated library it's one query, run weekly, grouped by team, returning a worklist. Which is the argument in miniature: the metadata was never the point, and neither was the database. Being able to ask was the point.

Keep the folder. Build the library.
None of this makes OKF a mistake, and I want to be precise about that, because “Markdown doesn’t scale” is the lazy reading and it’s wrong. The folder is the correct canonical layer at every scale: it is the only representation that is simultaneously human-writable, git-reviewable, vendor-neutral, and readable in fifty years. What changes with scale is the set of derived views you generate from it.

So the practical guidance writes itself. Adopt the folder now and keep it as the source of truth permanently. Treat v0.2’s new fields as your future query schema and populate them with the discipline Suganthan showed: a verified stamp nobody stands behind is worse than none. Automate your index.md early, because a hand-curated index is the first thing that silently rots. Add a generated search index the first time an agent wastes context hunting, and expect that day to arrive in the low thousands of concepts. Carry the graph-ready metamodel (stable IDs, typed links, a controlled type vocabulary) in your frontmatter from the beginning, because it costs pennies in YAML and a fortune in archaeology. And if your roadmap includes multimedia knowledge, budget for the graph and vector layer at the start, because for that content there is no earlier rung to stand on. When a second team wants in, agree the vocabulary before you agree anything else, and federate the bundles rather than merging them.

There’s a reason this ends at a library rather than a database. Collaboration is the trick our species is unreasonably good at: not speed, not strength, not individual brilliance, but the ability to pool what each of us knows and hand it forward intact. Every knowledge format ever invented is tooling for that one trick: the alphabet, double-entry bookkeeping, the citation, the shared drive, and now a folder of Markdown files with typed edges in the frontmatter.

What agents change is the throughput. An agent with a shared vocabulary, stable ids and a search index runs on rails: it can find what another team knows, judge how far to trust it, and see what it connects to, without interrupting anyone. An agent without them runs just as fast in every direction at once: inventing vocabulary, duplicating concepts, and citing something confidently that nobody has read since it was written.

The rails are the boring part. Two JSON files, an id you never change, a link with a name on it, a date you actually meant. Lay them and everything downstream (index, graph, library, agents) points the same way.

Google shipped a provenance release and, without quite saying so, published the schema for the knowledge graph everyone will eventually build behind it. The folder was enough. The folder is still enough, as the trunk, feeding everything that grows from it. And what grows from it was never going to be yours alone. That was always the point.