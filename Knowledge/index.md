# Index

Catalogue de la base. Une ligne par entrée. À relire en premier pour trouver une page,
puis descendre dans le fichier concerné. Mis à jour à chaque ingest.

## Inspirations

### agentic.md — agents, frameworks, MCP, harnesses
- **HuggingFace moonbot** — Slack bot sur harness Pi, mémoire persistante en HF Buckets, infra k8s.
- **Markets of agents** — marchés et conférences d'agents (ai4trade, agents4science).
- **Frameworks** — ClawCodex (rebuild Python de Claude Code), mini-swe-agent.
- **MCP andre.vote** — agent IA de sociologie électorale exposé comme connecteur MCP.
- **OKF: Open Knowledge Format** — format Markdown portable de contexte pour agents (concepts, metadata, index, logs).
- **Colab CLI** — délégation complète du fine-tuning à un agent sur Colab T4 depuis un seul prompt.
- **Flue** — framework TypeScript bâti sur le harness minimal Pi.
- **PI agents** — harnais d'agent minimal et atomique (pi-coding-agent, pi-agent-core, pi-ai).
- **AgentENV** — environnement d'exécution microVM (Firecracker) pour l'Agentic RL à grande échelle : snapshots incrémentaux, forks copy-on-write, réutilisation mémoire.
- **PRINCE (Bayer × Thoughtworks)** — étude de cas d'un agentic RAG + Text-to-SQL fiable en production sur des décennies de PDF précliniques ; context engineering + harness engineering, citations granulaires. → fulltext `Papers/martinfowler_reliable-agentic-ai.md`

### dataviz.md — visualisation, dashboards
- **Dataviz with DeckGL** — dataviz WebGPU haute densité (ex. réseau sémantique arXiv).

### kb.md — bases de connaissances, recherche, RAG
- **PageFind** — moteur de recherche statique sur fichiers HTML.
- **Airweave** — couche de recherche LLM auto-hébergée unifiant apps et bases.
- **Karpathy LLM-wiki** — patron du wiki markdown persistant maintenu par l'agent → fulltext `Papers/karpathy_llm-wiki.md`.
- **QMD** — moteur de recherche local sur markdown (BM25 + vecteur + re-rank LLM).
- **Graphify** — slash-command qui transforme un dossier (code, docs, images) en graphe de connaissances interrogeable, sans vector DB (Leiden sur la topologie) ; implémente le patron Karpathy en multimodal.
- **Deja** — prédicteur de commandes ZSH sans IA : 4 signaux (flou, fréquence×récence, répertoire courant, probabilité d'enchaînement) fondus en un score ; modèle de pertinence contextuelle frugal.

### library.md — bibliothèques/GLAM, OpenAlex, bibliométrie, patrimoine
- **OpenAlex snapshot** — snapshot OpenAlex en Parquet (DuckDB/Polars), filtrage par colonnes.
- **Reading Research Papers in the age of LLMs** — techniques de lecture augmentée → fulltext `Papers/medium_reading-research-papers.md`.

### llm.md — pré-entraînement / fine-tuning / architecture
- **VLM trained without image encoder** — VLM sans encodeur d'image (embeddings gérés côté décodeur).
- **World models** — modèles prédisant l'état plutôt que le token (Cosmos, Qwen AgentWorld, JEPA).
- **SLM pre-training pipeline** — synthèse complète du pré-entraînement SLM, incl. données synthétiques.
- **SLM extraction de propositions atomiques** — MPropositionneur-V2 (0.6B distillé), définition formelle des propositions atomiques.
- **HuggingFace ecosystem** — inférence/fine-tuning distant + gestion agentique via `hf` cli.
- **AirLLM** — inférence couche par couche (charger/calculer/libérer) : 70B sur 4 Go, 405B sur 8 Go, sans quantization ; goulot d'E/S disque. Ex. Kimi K3 (2.8T) sur un GPU 4 Go.

### llm-training.md — pré-entraînement / mid-training / post-training
- **Inkling-Small** — MoE open-weights 276B/12B actifs (Thinking Machines) qui égale Inkling à ¼ de la taille : on-policy distillation + RL, effort de raisonnement variable, multimodal encoder-free.
- **LLM from scratch** — ressources pédagogiques pour entraîner un (S)LM de bout en bout (LLM Builder app, notebooks Colab, repos GitHub) pour démos de formation.

### misc.md — divers
- **autoarxiv** — remplacer `arxiv`→`autoarxiv` dans l'URL pour lancer une repro par agent.
- **OSINT platforms** — plateformes OSINT temps réel (Crucix, Shadowbroker, Flowsint).
- **Blockchain / Ethereum / dApps** — piste exploratoire, connexions possibles avec LLM/embeddings.

### synthetic_data.md — données synthétiques
- **SynthTraces** — génération de traces d'agent synthétiques via harness Pi.
- **AutoData** — data scientist agentique pour données synthétiques de qualité.

## Papers (fulltext)
- **karpathy_llm-wiki.md** — « LLM Wiki » : le patron fondateur du wiki markdown maintenu par l'agent (Karpathy).
- **iaetbibliotheques_steering.md** — « Le Steering » : modifier le comportement d'un LLM sans fine-tuning (IA & Bibliothèques).
- **medium_fused-tiny-local-llms.md** — fusion active au niveau des logits de 3 petits LLM locaux.
- **medium_llm-rnn.md** — Memory Caching : mémoire croissante pour RNN, entre récurrence et attention pleine.
- **medium_reading-research-papers.md** — lire les papiers de recherche à l'ère des LLM (méthode en trois passes).
- **martinfowler_reliable-agentic-ai.md** — PRINCE (Bayer/Thoughtworks) : agentic RAG + Text-to-SQL en production, context & harness engineering, fiabilité et traçabilité.

## Idées
- **ideas_2026-07-31.md** — passe 1 (toute la base) : 6 idées CPU (vecteurs de steering portables, Lint frugal par propositions atomiques, URL-swap d'enrichissement de notices, fusion de logits pour catalogage, radar claim-delta OpenAlex, anti-bibliothèque du réfuté).
- **ideas_2026-08-01.md** — passe ciblée « pertinence sans IA » (déclencheur Deja) : 3 idées (autocomplétion UNIMARC/EAD par score fondu, RAG sans embeddings Deja×Graphify, curseur de flou recall/precision pour les autorités).

## Questions ouvertes
- **open_questions_2026-07-31.md** — 9 questions (composabilité du steering, steering vs fine-tuning, définition de contradiction, tokenizer partagé, world models comme mémoire d'agent, autorité des vedettes générées…).
- **open_questions_2026-08-01.md** — 5 questions (Markov des zones de catalogage, point de bascule features vs LLM, limites de la similarité sans embeddings, calibrage des crans de flou, demi-vie de récence en bibliothèque).

## Signaux faibles
- **weak_signals_2026-07-31.md** — 8 signaux (connaissance = artefact versionné, steering vs fine-tuning, propositions atomiques comme brique centrale, Pi couteau suisse, fusion runtime > merge, delta > temps réel, URL comme interface, le négatif est du signal).
- **weak_signals_2026-08-01.md** — 5 signaux (« pertinence sans vector DB » comme famille, le contexte courant comme feature, le compromis dans la main de l'usager, modèles de Markov d'actions, frugalité assumée comme position).

## Dossiers produits par l'agent
- **Syntheses/**, **Ideas/**, **Experiments/**, **OpenQuestions/**, **WeakSignals/**, **Resources/** — remplis par les passes de synthèse et d'idées (skill `inventor-ideas`).
