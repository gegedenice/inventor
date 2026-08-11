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

Couche de recherche auto-hébergée qui connecte apps, outils et bases, les synchronise en continu et les expose via une interface de recherche unifiée « LLM-friendly ».

Why is it interesting?
- self-hosted version

### Resources

- https://github.com/airweave-ai

### Takeaway

"Airweave connects to your apps, tools, and databases, continuously syncs their data, and exposes it through a unified, LLM-friendly search interface. 
AI agents query Airweave to retrieve relevant, grounded, up-to-date context from multiple sources in a single request."

### Questions

- Une couche de recherche unifiée sur les sources d'un établissement (SIGB, HAL, OpenAlex, mails) : quel périmètre réaliste et souverain ?

### Random Connections

- Smart Tool RAG d'OpenSpace (agentic.md) : même idée de recherche unifiée multi-sources pour agents.
- Idée « RAG sans embeddings » (Ideas/ideas_2026-08-01.md) : Airweave = la voie « couche de recherche managée », à comparer.
- PageFind / QMD (ce fichier) : briques de recherche plus légères.

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

---

## Compression de contexte pour LLM : du lisible (Nano-Capsulator) au model-native (BabelTele)

Deux papiers en lignée sur la **compression de contexte/prompt** — réduire tokens, latence et coût tout en préservant la sémantique et la transférabilité entre modèles. (1) **Nano-Capsulator** (Chuang et al., 2402) compresse en **langage naturel lisible** (« Capsule Prompt » : −81,4 % de longueur, jusqu'à 4,5× de latence en moins, transférable même sur LLM d'API). (2) **BabelTele** (Zhu et al., 2606) pousse la logique vers des **représentations non-lisibles par l'humain mais décodables par le LLM**.

Why is it interesting?
- Renversement radical (BabelTele) : « lisibilité humaine » et « décodabilité par le modèle » sont *découplables* — quand le lecteur est un autre modèle, on peut quitter le langage naturel pour du dense/symbolique/multilingue.
- Densité mesurée : **99,5 % de fidélité sémantique à 27,9 % du volume** (jusqu'à −72,1 % d'empreinte de contexte) — levier direct de frugalité (moins de tokens = moins de calcul).
- Nano-Capsulator compresse *sans soft-prompt* (fonction de reward + perte de préservation sémantique) → transférable, contrairement aux prompts continus.
- Tension féconde pour la bibliothèque : la valeur métier tient souvent à la *traçabilité/lisibilité humaine* (autorité, vérification) ; le model-native optimise l'inverse — où placer le curseur ?

### Resources

- Nano-Capsulator (2402.18700) — https://arxiv.org/abs/2402.18700 — fulltext (PDF) in @../Papers/2402.18700v2.pdf
- BabelTele (2606.19857) — https://arxiv.org/abs/2606.19857 — fulltext (PDF) in @../Papers/2606.19857v1.pdf

### Takeaway

"human readability, natural-language typicality, and model-side semantic recoverability can be partially decoupled (…) maintaining 99.5% semantic fidelity even when the text volume is condensed to 27.9% of its original length."

### Questions

- Compresser le *contexte* d'un agent bibliothécaire (notices, historique de session) pour tenir plus de fonds dans la fenêtre à moindre coût — **sans perdre le lien vers la source lisible** (traçabilité) ?
- Communication inter-agents en représentation compressée (mémoire/canal) : gain réel vs risque d'opacité et de débogage impossible ?
- Où le lisible reste-t-il *obligatoire* (réponse à l'usager, notice publiée) vs où le model-native est-il acceptable (caches, mémoire interne, comms machine↔machine) ?

### Random Connections

- SLM extraction de propositions atomiques (`llm.md`) : autre compression sémantique, mais *lisible et structurée* — opposé complémentaire de BabelTele.
- Acontext / progressive disclosure (`agentic.md`) + idée « RAG sans embeddings » (`../Ideas/ideas_2026-08-01.md`) : compresser le contexte plutôt que le récupérer.
- Steering / latent MLA (`llm.md`, `../Ideas/ideas_2026-08-03.md`) : BabelTele compresse au niveau *texte* ; le latent compresse au niveau *représentation* — deux étages du même geste.
- OKF (`agentic.md`) : parie sur le markdown lisible-humain ; BabelTele parie sur l'inverse — deux visions de « l'artefact que consomme l'agent ».

---

## Cycle de vie d'un knowledge graph : ghost nodes, tombstones, freshness gates (Graphify + OKF)

Retour d'expérience (Udaykiran Estari, Medium, 2026-07 — *article member-only : ici résumé depuis l'intro + le sommaire accessibles*) sur le point aveugle de Graphify/OKF : construire le graphe est facile, le **maintenir frais** ne l'est pas. Les mises à jour incrémentales (`--update`) laissent des *ghost nodes* — des nœuds pour du code (ou des entrées) supprimé — et l'agent devient « confidently wrong » sans alerte.

Why is it interesting?
- Déplace le sujet de « comment construire un knowledge graph » vers « comment l'empêcher de mentir au jour 45 » : dérive silencieuse, régressions de merge destructives, `--update` qui répond « already clean » alors que du périmé persiste.
- Propose un **cycle de vie** emprunté à la cohérence de cache : hachage de contenu, invalidation, **tombstones** (marquer le supprimé au lieu de l'effacer), **freshness gates**, et **reconstructions CI planifiées** (ne pas se fier au seul incrémental).
- Répond frontalement à la question « fraîcheur » qu'on avait posée sur OKF (« les fichiers ne se mettent pas à jour seuls ; le vrai test est la gouvernance »).

### Resources

- https://medium.com/@UdaykiranEstari/your-ai-coding-knowledge-graph-is-lying-to-you-graphify-okf-ac590c244158 (member-only ; corps complet non accédé — résumé depuis l'intro + TOC)

### Takeaway

"the knowledge graph it trusts still contains a node for a function your teammate deleted (…). Incremental updates reported 'already clean', but behind that green log line, stale nodes silently persisted."

### Questions

- Notre base `Knowledge/` a-t-elle ses propres *ghost nodes* (lignes d'index vers du supprimé, cross-links morts) ? C'est le rôle d'`inventor-lint` — faut-il y ajouter **tombstones** + une **freshness gate** ?
- Tombstones pour un catalogue : *marquer* une notice/autorité supprimée plutôt que l'effacer (traçabilité, cf. idée « anti-bibliothèque du réfuté ») ?
- Freshness gate + rebuild planifié : quand *reconstruire* une base documentaire qui grossit plutôt que l'incrémenter (au risque de la dérive) ?

### Random Connections

- Graphify (ce fichier) : cet article en est la critique « jour 45 » — le graphe dérive ; complète l'entrée Graphify.
- OKF (agentic.md) : outille sa question ouverte de *fraîcheur* (qui possède/rafraîchit le fichier après une réorg).
- Karpathy LLM-wiki (ce fichier) + skill `inventor-lint` : l'opération Lint = health-check ; tombstones + freshness gates en sont l'outillage concret.
- Idée « anti-bibliothèque / registre du réfuté » (`../Ideas/ideas_2026-07-31.md`) : les tombstones sont le mécanisme du « garder trace du supprimé ».
- Deja (récence, ce fichier) / claim-delta (claims qui disparaissent) : la fraîcheur/disparition comme signal de première classe.

---

## OKF v0.2 : le dossier a un plafond — l'échelle folder → index → search → graph

Analyse (David Oliver, Medium, 2026-07) de la v0.2 d'OKF (ajout de frontmatter *provenance / trust / lifecycle* + calcul attesté). Thèse : ces champs ne « paient » que si on peut les *interroger* à l'échelle du corpus — or un dossier de Markdown ne le peut plus passé un seuil. D'où une **échelle de vues dérivées**, chaque barreau forcé par la taille, le dossier restant canonique. Fulltext gardé en base.

Why is it interesting?
- **L'échelle** : dossier (l'agent marche l'arbre) → index curé (`index.md`) → **index de recherche généré** (BM25, puis hybride lexical+vecteur) → **property graph** (nœuds, arêtes *typées*, propriétés queryables → GraphRAG). Chaque vue est *dérivée* et jetable ; le Markdown reste la source de vérité.
- Déclencheur **comportemental, pas numérique** : on monte d'un barreau quand l'agent gâche son contexte en listings de répertoire / faux positifs grep (~bas milliers de concepts).
- **Design concret réutilisable** : index BM25 via **SQLite FTS5**, un seul `.db` portable, **reconstruit from scratch à chaque commit** (pas d'incrémental à rater → répond aux *ghost nodes*), chunké par *heading*, interrogé par un humain (`sqlite3`) ET un agent (outil MCP) — « un index, deux audiences ».
- **Métamodèle graph-ready dès le frontmatter** (spec-compatible) : `id` stable + `aliases`, **liens typés** (`rel: depends_on/supersedes…`) sortis de la prose, `type` comme label, valeurs scalaires typées, hash des binaires → migration future = « un loader de 100 lignes », pas une fouille.
- **Multimédia effondre l'échelle** : images/audio/vidéo échappent à grep/diff → vecteur + graphe *dès le jour 1*.
- **Fédérer, pas centraliser** : chaque équipe garde son bundle ; un « library process » synchronise et construit les vues dérivées sur l'union (jamais n'édite les fichiers) ; accord minimal = 2 fichiers JSON (vocabulaires de types et de relations).

### Resources

- https://medium.com/@davidroliver/okf-v0-2-quietly-admits-the-folder-has-a-ceiling-the-way-up-is-a-library-25fa54e872f9
- fulltext in @../Papers/medium_okf-v02-folder-ceiling.md

### Takeaway

"The folder isn't an alternative to a database. It is the larval form of one."

### Questions

- Notre `Knowledge/` approche-t-il le plafond du dossier ? Un index **FTS5/BM25 reconstruit à chaque commit**, interrogeable par `sqlite3` (humain) et MCP (agent), est-il le prochain barreau logique (cf. QMD, PageFind) ? → `../Experiments/exp_fts5_index_knowledge.md`
- Rendre notre métamodèle *graph-ready* : ajouter `id` stables + liens **typés** (transformer les « Random Connections » en arêtes `rel:`) pour qu'une future vue graphe soit un simple loader ?
- Fédération : plusieurs établissements gardant chacun leur bundle, un « library process » construisant index/graphe sur l'union — modèle pour un réseau documentaire (cf. OpenSpace cloud skill community, idée « notice à deux faces ») ?

### Random Connections

- OKF (agentic.md) : suite directe (v0.2) — répond à ses questions ouvertes de *fraîcheur* et de *passage à l'échelle*.
- Graphify (ce fichier) : le barreau « property graph » de l'échelle (convergence vers GraphRAG).
- Cycle de vie / ghost nodes (ce fichier) : « rebuild from scratch » = la réponse au périmé de l'incrémental.
- Karpathy LLM-wiki (ce fichier) : `index.md` + `log.md` = « forme larvaire d'une base » (index=retrieval, edges=relations, log=history) — l'article le formalise.
- QMD / PageFind (ce fichier) + idée « RAG sans embeddings » (`../Ideas/ideas_2026-08-01.md`) : FTS5/BM25 = pertinence lexicale sans vector DB, avant l'hybride.

---

## ContextGem — extraction structurée depuis documents, déclarative et référencée (long-contexte, anti-RAG assumé)

Framework open-source (Shcherbak AI, Apache-2.0) d'extraction structurée depuis des documents via LLM : on décrit **en langage naturel *quoi* extraire**, le framework gère le *comment* (prompts dynamiques, modèles de validation Pydantic, mapping des références, justifications, pipelines multi-aspects). Deux primitives : **Aspects** (segments/thèmes/sections) et **Concepts** (entités, faits, dates, ratings, objets JSON, booléens, conclusions). LiteLLM → cloud *et* local (Ollama, LM Studio) ; texte + vision ; storage sérialisable.

Why is it interesting?
- **Références précises (paragraphe/phrase) + justifications automatiques** : chaque donnée extraite pointe sa source dans le document et porte sa justification. Traçabilité native — exactement ce qu'exige une métadonnée/notice auditable.
- **Déclaratif, pas de prompt-engineering** : « you describe *what* to extract in natural language, and the framework handles *how* ». Abaisse le coût d'une chaîne d'extraction de notices (contrats, CV, factures, rapports → documents patrimoniaux) à quelques lignes.
- **Anti-RAG assumé pour le document unique** : exploite le **long contexte** au lieu de chunk+retrieve → capte les concepts subtils (ex. « anomalies » dans un contrat) que le RAG rate par fragmentation. Complète, ne remplace pas, un RAG corpus-wide (pas de requête cross-documents — pour ça, LlamaIndex/Haystack restent adaptés).
- **Pipelines d'extraction réutilisables et sérialisables** : un « profil » (aspects + concepts) rejouable à l'identique sur un lot de documents ; sauvegarde des résultats pour éviter de relancer des appels LLM coûteux.
- **Extraction hiérarchique** : aspects contenant des concepts, sous-aspects → structure riche en un seul passage. Recommande ≥ `gpt-4o-mini` (les petits modèles peinent sur ses instructions détaillées).

### Resources

- https://github.com/shcherbak-ai/contextgem
- https://contextgem.dev (docs) · https://deepwiki.com/shcherbak-ai/contextgem

### Takeaway

"You describe what to extract in natural language, and the framework handles how. [...] structured data with precise paragraph- and sentence-level references, automatic justifications, hierarchical multi-aspect extraction."

### Questions

- Un **pipeline ContextGem = un profil de notice rejouable** sur un fonds numérisé ? Les références paragraphe/phrase peuvent-elles matérialiser le lien *notice → source* (justifier chaque champ dans le document d'origine, EAD/Unimarc) ?
- **Long-contexte vs RAG pour l'enrichissement de notices** : où est le seuil (taille du document, coût des tokens) au-delà duquel le doc-entier (ContextGem) cède au chunk+retrieve ? Peut-on hybrider (RAG pour trouver le doc, ContextGem pour l'extraire) ?
- Le framework recommande ≥ gpt-4o-mini : nos briques CPU/SLM (Colibri, **Needle**) tiennent-elles la charge de ses « instructions détaillées », ou faut-il un gros modèle en local (Ollama) pour la partie extraction ? (cf. son guide *small models troubleshooting*)

### Random Connections

- **Needle** (`llm.md`) : pôle opposé du spectre extraction — ContextGem = gros LLM long-contexte, déclaratif, justifications+références ; Needle = SLM 14 Mo, grammaire byte-level, confidence, on-device. Même problème (« texte → JSON structuré »), deux stratégies à arbitrer selon coût/souveraineté/traçabilité.
- OCR de 30 000 papiers par un agent (`library.md`) : ContextGem est **l'étape d'après** — texte océrisé → champs structurés + références + justifications.
- SLM extraction de propositions atomiques (`llm.md`) : même finalité de structuration, approche framework-LLM vs SLM dédié.
- Compression de contexte / BabelTele (ce fichier) : ContextGem *mise* sur le long-contexte brut là où la compression le *réduit* — tension coût/latence à instrumenter.
- OKF v0.2 / métamodèle graph-ready + liens typés (ce fichier) : les concepts/références extraits par ContextGem = candidats naturels à des arêtes `rel:` typées et à un `id` stable.
