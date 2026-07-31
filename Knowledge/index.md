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

### dataviz.md — visualisation, dashboards
- **Dataviz with DeckGL** — dataviz WebGPU haute densité (ex. réseau sémantique arXiv).

### kb.md — bases de connaissances, recherche, RAG
- **PageFind** — moteur de recherche statique sur fichiers HTML.
- **Airweave** — couche de recherche LLM auto-hébergée unifiant apps et bases.
- **Karpathy LLM-wiki** — patron du wiki markdown persistant maintenu par l'agent → fulltext `Papers/karpathy_llm-wiki.md`.
- **QMD** — moteur de recherche local sur markdown (BM25 + vecteur + re-rank LLM).

### library.md — bibliothèques/GLAM, OpenAlex, bibliométrie, patrimoine
- **OpenAlex snapshot** — snapshot OpenAlex en Parquet (DuckDB/Polars), filtrage par colonnes.
- **Reading Research Papers in the age of LLMs** — techniques de lecture augmentée → fulltext `Papers/medium_reading-research-papers.md`.

### llm.md — pré-entraînement / fine-tuning / architecture
- **VLM trained without image encoder** — VLM sans encodeur d'image (embeddings gérés côté décodeur).
- **World models** — modèles prédisant l'état plutôt que le token (Cosmos, Qwen AgentWorld, JEPA).
- **SLM pre-training pipeline** — synthèse complète du pré-entraînement SLM, incl. données synthétiques.
- **SLM extraction de propositions atomiques** — MPropositionneur-V2 (0.6B distillé), définition formelle des propositions atomiques.
- **HuggingFace ecosystem** — inférence/fine-tuning distant + gestion agentique via `hf` cli.

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

## Dossiers produits par l'agent
- **Syntheses/**, **Ideas/**, **Experiments/**, **OpenQuestions/**, **Resources/** — vides pour l'instant, remplis par les passes de synthèse.
