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
