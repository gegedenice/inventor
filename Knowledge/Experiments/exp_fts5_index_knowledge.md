# Expérience : index FTS5/BM25 sur notre propre base Knowledge/ (dogfooding OKF v0.2)

**Hypothèse** : au-delà de quelques centaines d'entrées, marcher l'arbre `Knowledge/` (grep + lecture)
gaspille le contexte de l'agent ; un **index BM25 via SQLite FTS5**, reconstruit *from scratch* à chaque
commit, chunké par heading, donne une récupération nettement meilleure et moins coûteuse — tout en
gardant le Markdown comme unique source de vérité (l'index est jetable).

**Protocole minimal** :
1. Script Python (stdlib) qui parcourt `Knowledge/**/*.md`, découpe par `##`/`###`, insère dans une table
   FTS5 `chunks(path UNINDEXED, heading, content, tokenize='porter unicode61')` → un seul `knowledge.db`.
2. Requête BM25 avec poids `(0.0, 8.0, 1.0)` (boost heading) + `snippet()`.
3. Deux accès : `sqlite3` (humain) et un mini-serveur MCP `search_docs(query, top_k)` (agent).
4. Hook post-commit / CI : drop + rebuild.
5. Évaluer sur ~15 requêtes réelles (« streaming d'experts », « steering latent », « fraîcheur du wiki »…) :
   comparer top-5 FTS5 vs grep vs embeddings (bge-m3) — pertinence jugée à la main + coût (tokens/temps).

**Métrique visée** : FTS5 bat nettement grep (moins de faux positifs, snippets ciblés) et approche
l'embedding sur les requêtes lexicales, à coût quasi nul (pas de serveur, pas de ré-embedding).

**Faiblesses / risques** : BM25 rate la similarité purement sémantique (paraphrases) → prévoir l'étape
hybride ensuite ; qualité du chunking = qualité de l'index ; petit corpus actuel (le gain croît avec la taille).

**Contraintes** : CPU only, zéro dépendance lourde (SQLite FTS5 est dans la stdlib). Directement dogfoodable
sur ce dépôt — et brique possible pour `inventor-lint` (health-check) et `inventor-ideas` (récupération).

**Source** : `../Inspirations/kb.md` (OKF v0.2 folder-ceiling) + `../Papers/medium_okf-v02-folder-ceiling.md` (design FTS5) ;
`../Inspirations/kb.md` QMD/PageFind ; idée « RAG sans embeddings » (`../Ideas/ideas_2026-08-01.md`).
