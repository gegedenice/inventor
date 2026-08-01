# Knowledge base management

## PageFind

Why is it interesting?
- Simple and quick search engine on HTML files

### Resources

- https://pagefind.app/

### Takeaway

"After indexing, Pagefind adds a static search bundle to your built files, which exposes a JavaScript search API that can be used anywhere on your site. Pagefind also provides prebuilt UI components that can be used with no configuration."

### Questions

### Random Connections

## Airweave

Why is it interesting?
- self-hosted version

### Resources

- https://github.com/airweave-ai

### Takeaway

"Airweave connects to your apps, tools, and databases, continuously syncs their data, and exposes it through a unified, LLM-friendly search interface. 
AI agents query Airweave to retrieve relevant, grounded, up-to-date context from multiple sources in a single request."

---

## Karpathy LLM-wiki

Le patron fondateur : au lieu d'un RAG qui redécouvre tout à chaque requête, l'agent construit et maintient un wiki markdown persistant et interconnecté. La connaissance est compilée une fois puis tenue à jour, pas re-dérivée à chaque question.

Why is it interesting?
- Le wiki est un artefact qui se compose dans le temps (cross-refs déjà là, contradictions déjà signalées) — c'est exactement la mission « compresser, connecter » d'AGENTS.md.
- Trois couches nettes : sources brutes (immuables) / wiki (écrit par l'agent) / schema (AGENTS.md-CLAUDE.md qui dicte les conventions) — c'est la structure d'Inventor.
- Trois opérations : Ingest / Query / Lint. Le skill `ingest` couvre la 1re ; `Lint` reste à outiller (health-check : contradictions, pages orphelines, cross-refs manquants).
- index.md (catalogue) + log.md (append-only, préfixe daté grep-able) suffisent jusqu'à ~100 sources — pas besoin d'embeddings.

### Resources

- Karpathy LLM-wiki: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- Implementations: https://blog.stackademic.com/rag-is-dead-llm-wiki-andrej-karpathys-idea-is-what-comes-next-a71fa3c414a4
- fulltext in @../Papers/karpathy_llm-wiki.md

### Takeaway

"Instead of just retrieving from raw documents at query time, the LLM incrementally builds and maintains a persistent wiki (…). The knowledge is compiled once and then kept current, not re-derived on every query. (…) The tedious part of maintaining a knowledge base is not the reading or the thinking — it's the bookkeeping. (…) LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass."

### Questions

- Notre `Knowledge/` manque encore `index.md` et `log.md` : les ajouter pour rendre la base navigable et l'historique grep-able ?
- L'opération `Lint` de Karpathy n'est pas outillée chez nous — en faire une passe planifiée sur le VPS (santé du wiki : orphelins, contradictions, cross-refs manquants) ?
- Karpathy sépare `raw sources` immuables du `wiki` généré. Nos `Papers/` (fulltext) jouent le rôle de raw, `Inspirations/` celui de wiki : faut-il rendre cette séparation explicite ?

### Random Connections

- QMD (plus bas dans ce fichier) est l'outil de recherche que Karpathy recommande explicitement pour ce patron — brique naturelle de la couche « CLI tools ».
- PageFind / Airweave (ce fichier) sont des alternatives à la couche recherche du wiki.
- Le principe « les bonnes réponses sont refilées dans le wiki » rejoint les dossiers `Syntheses/` et `Ideas/` d'Inventor.
- « Reading Research Papers in the age of LLMs » (library.md) partage l'idée d'une lecture augmentée qui alimente une base cumulative.

---

## QMD - Query Markup Documents

Why is it interesting?
- Simple and quick search engine on HTML files

### Resources

- https://github.com/tobi/qmd

### Takeaway

"QMD combines BM25 full-text search, vector semantic search, and LLM re-ranking—all running locally via node-llama-cpp with GGUF models."

### Questions

### Random Connections

---

## Graphify — un dossier → un graphe de connaissances interrogeable

Skill open-source (Safi Shamsi, licence MIT, avril 2026) invoquée comme slash-command `/graphify` dans les assistants de code (Claude Code, Codex, OpenClaw, Trae…) : elle lit tous les fichiers d'un dossier — code, PDF, markdown, captures, diagrammes, photos de tableau — et en construit un graphe de connaissances interrogeable, sans base vectorielle. Née en 48 h d'un besoin décrit par Karpathy (son dossier `/raw`).

Why is it interesting?
- Reprend la structure à trois couches de Karpathy (raw immuable / wiki généré / schema) — exactement celle d'Inventor — mais l'automatise en graphe multimodal.
- Pas d'embeddings ni de vector DB : la topologie du graphe EST le signal de similarité ; clustering par algorithme de Leiden sur la densité des liens. Renverse l'hypothèse RAG « il faut des embeddings ».
- Analyse statique locale (Tree-sitter AST) pour le code — rien ne quitte la machine ; seules des descriptions sémantiques partent vers l'API du modèle pour docs/images.
- Chaque relation est étiquetée EXTRACTED / INFERRED (avec score) / AMBIGUOUS — on sait ce que le graphe a *trouvé* vs *deviné*. Transparence rare dans un espace de boîtes noires.
- Sortie réutilisable : `graph.html` interactif, `GRAPH_REPORT.md` (« god nodes », connexions inattendues, questions suggérées), `graph.json` requêtable des semaines plus tard, cache SHA256 (re-run ne touche que les fichiers changés). Options : vault Obsidian + wiki avec `index.md`.
- Chiffre viral : 71,5× moins de tokens par requête vs lecture brute des fichiers (corpus mixte repos + papiers + images ; bench surtout parlant au-delà de ~50 fichiers).

### Resources

- https://www.towardsdeeplearning.com/andrej-karpathy-asked-for-a-tool-48-hours-later-graphify-went-viral-10d8ead5f50e
- Repo : https://github.com/safishamsi/graphify
- Explainer : https://aitrovex.com/blog/what-is-graphify
- Site + dérivé Penpax (graphe personnel on-device) : https://graphify.net/

### Takeaway

"It reads every file in your folder — code, PDFs, Markdown docs, screenshots, diagrams, even whiteboard photos — and builds a queryable knowledge graph that shows you the structure, relationships, and 'why' behind everything."

### Questions

- Lancer Graphify sur notre `Knowledge/` (tenu à la main) donnerait-il un `GRAPH_REPORT` (god nodes, connexions inattendues) utile pour la passe d'idées et le Lint ?
- « La topologie EST la similarité » (Leiden, sans embeddings) : alternative frugale CPU à claim-delta (qui embarque bge-m3) pour relier propositions/notices ?
- Étiquetage EXTRACTED / INFERRED / AMBIGUOUS : transférable au catalogage pour tracer l'autorité (métadonnée extraite vs inférée par un SLM) ?

### Random Connections

- Karpathy LLM-wiki (ce fichier → `Papers/karpathy_llm-wiki.md`) : Graphify est l'implémentation automatisée et multimodale de son patron à trois couches ; le wiki + `index.md` qu'il produit rejoint exactement notre `Papers/`.
- QMD / PageFind / Airweave (ce fichier) : couches de recherche ; Graphify remplace la recherche par la navigation de graphe (pas de vector DB) — même famille « connaissance compilée, pas re-dérivée ».
- OKF (agentic.md) : Graphify sort un wiki markdown + `index.md` navigable par agent — un producteur possible de bundles quasi-OKF.
- Idée « Lint frugal » (Ideas/ideas_2026-07-31.md) : les god nodes et connexions inattendues de Graphify sont une autre voie vers le health-check du wiki.
- Hook `PreToolUse` (lit `GRAPH_REPORT.md` avant chaque Glob/Grep) : rejoint le principe AGENTS.md « lire l'index d'abord ».
---

## Deja — prédiction de commande sans IA (4 signaux fondus)

Prédicteur de commandes ZSH (remplace zsh-autosuggestions) qui devine la prochaine commande **sans aucune IA**, par pure algorithmie : quatre signaux fondus en un score unique — correspondance floue, fréquence combinée à la récence (demi-vie 1 semaine), affinité avec le répertoire courant, probabilité d'enchaînement. Acceptation à la flèche droite. MIT, zsh, macOS/Linux.

Why is it interesting?
- Pertinence sans modèle ni embeddings : 4 features simples + une somme pondérée suffisent pour une prédiction locale à coût nul. Renverse « il faut un LLM pour l'autocomplétion ».
- Le contexte comme feature de premier plan : le *répertoire courant* conditionne la suggestion — le lieu/l'état pèse autant que l'historique.
- Probabilité d'enchaînement (ex. `make test` après `make build`) = modèle de Markov d'ordre 1 sur les séquences d'actions, ultra-léger.
- Le flou est un curseur exposé à l'utilisateur (tight ≤1 / smart ≤4 / loose ≤8), pas un hyperparamètre caché : la tolérance recall/precision est mise dans la main de l'usager.

### Resources

- https://korben.info/deja-terminal-predictif.html
- Repo : https://github.com/Giammarco-Ferranti/deja

### Takeaway

"Les quatre signaux, correspondance floue, fréquence combinée à la récence avec une demi-vie d'une semaine, affinité avec le répertoire courant et probabilité d'enchaînement, sont fondus dans un score unique."

### Questions

- Transposable à l'autocomplétion de notices (UNIMARC/EAD) : zone courante + historique du catalogueur + probabilité d'enchaînement de champs comme features, sans IA ?
- Le curseur de flou (tight/smart/loose) : utile pour une recherche bibliographique tolérante aux fautes de frappe / translittérations, réglable à la volée ?

### Random Connections

- QMD / PageFind (ce fichier) : recherche locale ; Deja ajoute la dimension prédiction/ranking contextuel sans embeddings.
- Graphify (ce fichier) : « la topologie EST la similarité » ; Deja « des features simples fondues en un score » — même famille « pertinence sans vector DB ».
- Idée « Lint frugal » / MPropositionneur (Ideas/ideas_2026-07-31.md, llm.md) : même esprit « features simples > LLM » sur des tâches structurées.
