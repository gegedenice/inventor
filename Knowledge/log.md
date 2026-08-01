# Log

Journal chronologique, append-only. Chaque entrée commence par `## [AAAA-MM-JJ] <type> | <sujet>`
pour rester grep-able : `grep "^## \[" log.md | tail -5` donne les 5 dernières.
Types : `ingest`, `query`, `lint`, `refactor`.

## [2026-07-31] refactor | Préparation de Knowledge pour git
Ajout de index.md (catalogue), log.md (ce fichier), .gitignore et .gitkeep dans les dossiers
vides (Syntheses, Ideas, Experiments, OpenQuestions, Resources) pour que git suive l'arborescence.
Ajout du skill `.skills/ingest/SKILL.md` (workflow « donne une URL, l'agent range »).

## [2026-07-31] ingest | Karpathy — LLM Wiki
Source : https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
Entrée existante (vide) dans Inspirations/kb.md enrichie au lieu d'être dupliquée ; fulltext
extrait dans Papers/karpathy_llm-wiki.md. Cross-links posés vers QMD, PageFind, Airweave.

## [2026-07-31] ingest | OKF — analyse critique (Rahmat)
Source : https://medium.com/google-cloud/open-knowledge-format-the-missing-layer-for-context-aware-ai-agents-3694b1c1df1c
Doublon de l'entrée OKF existante dans Inspirations/agentic.md (source primaire = blog Google Cloud) :
entrée enrichie plutôt que dupliquée (description, why, 4 questions de scepticisme, cross-links concrets).
Cross-links : Karpathy LLM-wiki (kb.md/Papers), QMD (kb.md), la base Knowledge/ elle-même comme bundle quasi-OKF.

## [2026-07-31] ideas | passe toute la base
6 idées, 9 questions, 8 signaux. Meilleure piste : Lint frugal du wiki via propositions
atomiques (MPropositionneur), directement dogfoodable sur ce dépôt.
Fichiers : Ideas/ideas_2026-07-31.md, OpenQuestions/open_questions_2026-07-31.md, WeakSignals/weak_signals_2026-07-31.md.

## [2026-07-31] ingest | AgentENV — environnement d'exécution pour Agentic RL
Source : https://kvcache.ai/blog/agentenv-open-sourced
Classée dans Inspirations/agentic.md (nouvelle entrée).
Cross-links : SynthTraces (synthetic_data.md), PI agents (agentic.md), autoarxiv (misc.md).

## [2026-07-31] ingest | Graphify — dossier → graphe de connaissances
Source : https://www.towardsdeeplearning.com/andrej-karpathy-asked-for-a-tool-48-hours-later-graphify-went-viral-10d8ead5f50e
Page primaire rendue en JS (fetch vide, Chrome non connecté) : contenu recoupé via aitrovex + recherche web.
Classée dans Inspirations/kb.md (nouvelle entrée).
Cross-links : Karpathy LLM-wiki + QMD/PageFind/Airweave (kb.md), OKF (agentic.md), idée Lint frugal (Ideas/).

## [2026-08-01] ingest | Deja — prédiction de commande sans IA
Source : https://korben.info/deja-terminal-predictif.html
Classée dans Inspirations/kb.md (nouvelle entrée).
Cross-links : QMD/PageFind/Graphify (kb.md), idée Lint frugal (Ideas/).

## [2026-08-01] ideas | passe ciblée "pertinence sans IA" (déclencheur Deja)
3 idées, 5 questions, 5 signaux. Meilleure piste : autocomplétion de notices UNIMARC/EAD par
score fondu (4 signaux Deja) sans IA, calibré sur l'historique local du catalogue.
Fichiers : Ideas/ideas_2026-08-01.md, OpenQuestions/open_questions_2026-08-01.md, WeakSignals/weak_signals_2026-08-01.md.

## [2026-08-01] ingest | PRINCE — agentic RAG fiable en production (Bayer × Thoughtworks)
Source : https://martinfowler.com/articles/reliable-llm-bayer.html
Classée dans Inspirations/agentic.md (entrée-pointeur) + fulltext Papers/martinfowler_reliable-agentic-ai.md.
Cross-links : AgentENV + OKF (agentic.md), Karpathy LLM-wiki (kb.md), idées RAG-sans-embeddings/autocomplétion (Ideas/2026-08-01).

## [2026-08-01] ingest | Inkling-Small (Thinking Machines) + ré-indexation
Source : https://thinkingmachines.ai/news/inkling-small/
Complète l'entrée-stub ajoutée à la main dans Inspirations/llm-training.md (nouveau fichier thématique).
Ré-indexation : ajout de la rubrique llm-training.md (Inkling-Small, LLM from scratch) dans index.md ;
vérif de la couverture de tout index.md vs les fichiers Inspirations/Papers (à jour).
Cross-links : VLM sans encodeur + MPropositionneur (llm.md), Deja + curseur recall/precision (kb.md, Ideas/), dataviz radar.

## [2026-08-01] chore | ajout manuel de ressources (utilisateur)
Enrichissement de la ressource Shadowbroker dans Inspirations/misc.md (entrée OSINT platforms existante).

## [2026-08-01] ingest | AirLLM — inférence couche par couche (Kimi K3 sur GPU 4 Go)
Source : https://ai.gopubby.com/unbelievable-run-kimi-k3-2-8-trillion-parameters-on-a-single-4gb-gpu-23590e7a16c2
Page primaire rendue en JS (fetch vide, Chrome non connecté) : technique recoupée via README AirLLM + recherche.
Classée dans Inspirations/llm.md (nouvelle entrée).
Cross-links : HF moonbot + HF ecosystem/hf-mount (stockage), Inkling-Small MoE (llm-training.md), Memory Caching (Papers/).

## [2026-08-01] ideas | passe 2 (déclencheur AirLLM / Kimi K3)
3 idées, 3 questions, 2 signaux (append dans les fichiers 2026-08-01). Meilleure piste (idée utilisateur) :
inférence AirLLM avec shards de couches en HF buckets/hf-mount — découpler stockage et compute.
