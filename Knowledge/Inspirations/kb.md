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

