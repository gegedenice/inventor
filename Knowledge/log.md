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

## [2026-08-01] ingest | Orca — cockpit d'agents parallèles (fan-out / pick the winner)
Source : https://githubdaily.medium.com/my-experience-migrating-from-cmux-to-orca-a-powerful-tool-for-multi-agent-parallel-verification-37e662b8c2c5
Article member-only (intro seule) : faits sur Orca recoupés via repo stablyai/orca + recherche.
Classée dans Inspirations/agentic.md (nouvelle entrée).
Cross-links : AgentENV (fan-out/isolation), idée COW-fork via git worktrees (Ideas/2026-07-31), PRINCE, PI agents.

## [2026-08-01] ideas | passe 3 (déclencheur Orca)
2 idées, 3 questions, 3 signaux (append dans les fichiers 2026-08-01). Meilleure piste :
répliquer la logique fan-out/comparer/fusionner d'Orca dans une UI « à cartes » non-terminal pour tâches biblio.
Question de fond retenue : l'interface IA « ultime » = cockpit d'agents parallèles adapté aux workflows documentaires ?

## [2026-08-01] ingest | Colibri — runtime MoE frugal (experts streamés du disque)
Source : https://github.com/JustVugg/colibri
Classée dans Inspirations/llm.md (nouvelle entrée).
Cross-links : AirLLM + idée MoE×AirLLM (Ideas/2026-08-01 passe 2), Inkling-Small/Kimi K3 (MoE), DeckGL (dataviz.md), Graphify (kb.md).

## [2026-08-01] ingest | SkillOpt — entraîner le skill (texte), pas les poids
Source : https://github.com/microsoft/SkillOpt
Classée dans Inspirations/agentic.md (nouvelle entrée).
Cross-links : Steering (llm.md/Papers) + idée vecteurs-de-steering-comme-skills (Ideas/2026-07-31), Karpathy LLM-wiki (kb.md), OKF (agentic.md).

## [2026-08-01] ingest | OpenSpace — skills auto-évolutifs + communauté de partage
Source : https://github.com/HKUDS/OpenSpace
Classée dans Inspirations/agentic.md (nouvelle entrée).
Cross-links : SkillOpt (agentic.md, complémentaire), Karpathy LLM-wiki (kb.md), Markets of agents/PI packages, Smart Tool RAG↔QMD (kb.md).

## [2026-08-01] ingest | autoarxiv (post LinkedIn) — doublon, entrée enrichie
Source : https://x.com/akshay_pachaar/status/2068672842733109327 (repris en post LinkedIn)
Doublon de l'entrée existante Inspirations/misc.md#autoarxiv : enrichie (provider alphaXiv, écosystème OpenResearch, Resources, Questions, Random Connections). Takeaway inchangé.
Cross-links : idée URL-swap (Ideas/2026-07-31), AgentENV/Orca (agentic.md), Colibri/AirLLM (llm.md).

## [2026-08-01] ingest | OpenWorker — coworker local-first qui livre du travail fini
Source : https://github.com/andrewyng/openworker
Classée dans Inspirations/agentic.md (nouvelle entrée).
Cross-links : Orca/OpenSpace/PI (interface d'agents), PRINCE (approbation humaine), aisuite↔pi-ai, question "interface ultime" (OpenQuestions passe 3).

## [2026-08-01] ingest | Prompt Cartography — Design Schemas
Source : https://promptcartography.com/blog/design-schemas-have-arrived.html
Classée dans Inspirations/dataviz.md (nouvelle entrée).
Cross-links : DeckGL (dataviz.md), OKF (agentic.md), SkillOpt/OpenSpace (skills-as-artifacts), Steering (llm.md/Papers).

## [2026-08-01] ingest | OCR de 30 000 papiers par un agent (Codex + HF Jobs + hf-mount)
Source : https://huggingface.co/blog/nielsr/ocr-papers-jobs
Classée dans Inspirations/library.md (nouvelle entrée).
Cross-links : HuggingFace ecosystem (llm.md), idée AirLLM×HF-buckets (Ideas/2026-08-01), autoarxiv (misc.md), Colibri/AirLLM (llm.md).

## [2026-08-02] refactor | Lentille opérationnelle (état de l'art + expériences)
Ajout de la couche StateOfTheArt/ (pretraining, posttraining, synthetic_data, inference_archi) —
fiches vivantes maintenues en place. Nouveau skill inventor-lab. inventor-ingest gagne la
lentille opérationnelle (MàJ fiche SOTA + candidat Experiments/ pour les sources techniques).
Routage : llm.md (archi/inférence) vs llm-training.md (pré/mid/post-training). AGENTS.md documente
les deux lentilles et les trois skills.

## [2026-08-02] lab | revue toute la base (1re passe)
4 fiches SOTA peuplées, 4 idées appliquées, 5 expériences proposées. Priorité : enrichissement
de notices nocturne via Colibri CPU-only (souveraineté, coût API nul), à valider par un bench de débit.
Fichiers : StateOfTheArt/*.md, Ideas/applied_ideas_2026-08-02.md, Experiments/exp_*.md.

## [2026-08-02] lint | reindex + health-check
0 entrée ajoutée, 0 orpheline retirée (index déjà cohérent : 62 entrées, dont Expériences×5 et État de l'art×4 de la passe lab).
6 constats de santé : 2 stubs (Markets of agents, Frameworks), 4 orphelines de cross-link (moonbot, Markets, Frameworks, Airweave, + OpenWorker entrante). Voir Syntheses/lint_2026-08-02.md.

## [2026-08-02] lint | correctifs santé (stubs + cross-links)
Complété les 2 stubs (Markets of agents, Frameworks : description + « Why ») et posé les cross-links sûrs :
moonbot→PI/HF-buckets, Frameworks→PI/Flue/OpenClaw, Markets→OpenSpace/PI packages, Orca→OpenWorker, Airweave (description + Questions + Random Connections). Résout les orphelines du rapport lint_2026-08-02.

## [2026-08-02] ingest | Acontext — les skills comme couche de mémoire d'agent
Source : https://github.com/memodb-io/Acontext
Classée dans Inspirations/agentic.md (nouvelle entrée). Lentille opérationnelle : non (infra mémoire, hors SOTA pré/post-training/synth/inférence).
Cross-links : SkillOpt + OpenSpace (skills auto-évolutifs), Karpathy LLM-wiki (kb.md), OKF, idée RAG-sans-embeddings.
index.md : laissé à inventor-lint.

## [2026-08-02] lint | reindex + health-check
1 entrée ajoutée (Acontext -> agentic.md), 0 retirée. Health : stubs (Markets, Frameworks) et orphelines du rapport précédent résolus ; Acontext déjà bien cross-linké. Voir Syntheses/lint_2026-08-02b.md.

## [2026-08-03] ingest | Kimi K3 — architecture d'efficacité (LatentMoE, KDA+MLA, QAT FP4)
Source : document utilisateur (analyse Kimi K3, type alpha_signal).
Classée dans Inspirations/llm.md (entrée-pointeur) + fulltext Papers/kimi3_architecture_efficiency.md.
Lentille opérationnelle : StateOfTheArt/inference_archi.md (KDA état fixe, LatentMoE, NoPE/AttnRes) + posttraining.md (QAT natif FP4) MàJ ; candidats Experiments/exp_steering_etat_recurrent.md + exp_qat_fp4_slm_biblio.md.
Cross-links : Memory Caching (Papers), Colibri/AirLLM (llm.md), Steering (Papers) + idée vecteurs de steering.
index.md : laissé à inventor-lint.

## [2026-08-03] ideas | passe déclencheur Kimi K3 (attention linéaire / latent / QAT)
3 idées, 5 questions, 5 signaux. Meilleure piste (angle utilisateur) : steering de l'état récurrent d'une
attention linéaire (biais persistant à coût constant), + loger les vecteurs de steering dans le latent compressé (LatentMoE/MLA).
Fichiers : Ideas/ideas_2026-08-03.md, OpenQuestions/open_questions_2026-08-03.md, WeakSignals/weak_signals_2026-08-03.md.
index.md : laissé à inventor-lint.

## [2026-08-03] ingest | kimi-k3-in-c — Kimi K3 en C99 sur 1 CPU (8,24 Go)
Source : https://github.com/FareedKhan-dev/kimi-k3-in-c
Classée dans Inspirations/llm.md (nouvelle entrée). Lentille opérationnelle : StateOfTheArt/inference_archi.md MàJ (moteur MoE mono-fichier C99, MXFP4 sans déquant.) + candidat Experiments/exp_moe_streaming_midsize.md.
Cross-links : AirLLM + Colibri (union), Kimi K3 architecture (Papers), idée MoE×AirLLM.
index.md : laissé à inventor-lint.

## [2026-08-03] ideas | passe 2 (déclencheur kimi-k3-in-c)
2 idées, 3 questions, 3 signaux (append dans les fichiers 2026-08-03). Réponses : (1) réplicable mid-size = oui, plus rapide (moins d'experts) — expérience cadrée ; (2) kimi-k3-in-c = union AirLLM (streaming couches) + Colibri (streaming experts) en C99. Meilleure piste : moteur MoE mid-size souverain en C.

## [2026-08-03] ingest | Compression de contexte LLM : Nano-Capsulator (2402.18700) -> BabelTele (2606.19857)
Sources : https://arxiv.org/abs/2402.18700 , https://arxiv.org/abs/2606.19857 (PDF fournis).
Rangement : PDFs primaires déplacés de Resources/ vers Papers/ (Papers = fulltext/sources ; Resources = datasets/poids/gros dumps). Entrée dans Inspirations/kb.md pointant les 2 PDFs.
Lentille opérationnelle : StateOfTheArt/inference_archi.md MàJ (compression de contexte / model-native) + candidat Experiments/exp_compression_contexte_notices.md.
Cross-links : propositions atomiques (llm.md), Acontext/RAG-sans-embeddings, steering/latent, OKF.
index.md : laissé à inventor-lint.

## [2026-08-03] ideas | passe 3 (déclencheur Nano-Capsulator -> BabelTele)
2 idées, 3 questions, 3 signaux (append aux fichiers 2026-08-03). Meilleure piste : "model-native dedans, lisible dehors" (contexte d'agent comprimé, sortie toujours traçable) — concilie frugalité et traçabilité biblio.

## [2026-08-03] synthese | Spectre lisible ↔ model-native (transversal)
Note transversale Syntheses/synthese_spectre_lisible_modelnative.md reliant compression de contexte (kb.md), skills (agentic.md), inférence/latent (llm.md, Papers Kimi K3). Cross-links ajoutés depuis llm.md (MPropositionneur) et agentic.md (SkillOpt). index.md : laissé à inventor-lint.

## [2026-08-03] ideas | passe 4 (portée large de la compression : inférence, skills, latent)
2 idées, 3 questions, 3 signaux. Meilleure piste : sonde latente prompt vs BabelTele (Experiments/exp_sonde_latente_babeltele.md) — expérience minimale retenue. + compression de skills (source lisible + capsule dérivée).

## [2026-08-03] lint | reindex + health-check
11 entrées ajoutées (Compression de contexte + Kimi K3 + kimi-k3-in-c ; 3 Papers ; ideas/OQ/WS 2026-08-03 ; 5 Experiments ; section Synthèses), 0 retirée. Santé : nouvelles entrées bien cross-linkées, axe transversal « spectre lisible↔model-native » émergent. Voir Syntheses/lint_2026-08-03.md.

## [2026-08-05] ingest | Soup — fine-tuning/post-training LLM en une commande
Source : https://github.com/MakazhanAlpamys/Soup
Classée dans Inspirations/llm-training.md (nouvelle entrée). Lentille opérationnelle : StateOfTheArt/posttraining.md MàJ (orchestration one-command + offloading mémoire « streaming train » + garde-fou reward-hacking) + candidat Experiments/exp_soup_finetune_slm_biblio.md.
Angle utilisateur : « layer streaming mais pour l'entraînement ; streaming RAM/VRAM = le nouveau trend » — capté (activation offloading/GaLore/DeepSpeed = pendant train d'AirLLM/Colibri).
Cross-links : Colab CLI (agentic.md), AirLLM/Colibri/kimi-k3-in-c (llm.md), Inkling-Small + QAT (llm-training/Papers).
index.md : laissé à inventor-lint.

## [2026-08-08] ingest | Cycle de vie d'un knowledge graph (ghost nodes, tombstones) — Graphify + OKF
Source : https://medium.com/@UdaykiranEstari/your-ai-coding-knowledge-graph-is-lying-to-you-graphify-okf-ac590c244158 (member-only : résumé depuis intro + TOC seulement).
Classée dans Inspirations/kb.md (nouvelle entrée). Pas de lentille opérationnelle (maintenance de KB, hors SOTA training/inférence).
Cross-links : Graphify + Karpathy LLM-wiki (kb.md) + inventor-lint, OKF (agentic.md, question fraîcheur), idée anti-bibliothèque (Ideas/2026-07-31).
index.md : laissé à inventor-lint.
