# État de l'art — Génération de données synthétiques

> Dernière mise à jour : 2026-08-02 · Maintenu par la skill `inventor-lab`
> **Fiche vivante** : mise à jour *en place* à chaque source pertinente. On révise, on n'empile pas.
> Contraintes du contexte : CPU only, mémoire réduite (cf. `../00_research_notes.md`).

## En bref
La tendance dominante : la génération de données synthétiques est **pilotée par des agents**
(harnais où des modèles dialoguent ou explorent un environnement) plutôt que par des prompts
one-shot. Sous contrainte frugale, le point clé est de mettre le **petit modèle local** du
bon côté du harnais (ex. jouer l'utilisateur) et de déléguer la partie lourde à distance.

## Techniques / approches clés
_Format : technique — statut — quand l'utiliser — source(s)._

- **Harnais agentique de traces (SynthTraces)** — émergent — générer des traces d'agent/Q&A : un modèle ouvert joue l'agent (avec accès read+bash à un vrai codebase), un petit modèle local (llama.cpp) joue l'utilisateur. — `../Inspirations/synthetic_data.md`
- **Data scientist agentique (AutoData)** — émergent — produire des données synthétiques de haute qualité via un agent. — `../Inspirations/synthetic_data.md`
- **Données synthétiques dans le pipeline de pré-training** — émergent — voir `pretraining.md`. — `../Inspirations/llm.md`
- **Données on-policy issues de la distillation (Inkling-Small)** — émergent — la distillation on-policy génère de fait des données alignées sur la politique de l'élève. — `../Inspirations/llm-training.md`

## Ce qui a bougé récemment
- [2026-08-02] Première population depuis `Inspirations/`. Fil conducteur repéré : le **harnais Pi** revient comme couteau suisse (SynthTraces) — brique réutilisable pour générer des jeux de données domaine (biblio) à faible coût côté modèle local.

## Questions ouvertes / à trancher
- Un harnais type SynthTraces peut-il générer un jeu Q/R de catalogage (UNIMARC/EAD) exploitable, avec le petit modèle local en « utilisateur » ? (→ `../OpenQuestions/`)

## Candidats d'expériences
- `../Experiments/exp_synthdata_biblio_harness.md`

## Sources dans la base
- **SynthTraces**, **AutoData** — `../Inspirations/synthetic_data.md`
- **SLM pre-training pipeline** (inclut données synthétiques) — `../Inspirations/llm.md`
- **Inkling-Small** (on-policy distillation) — `../Inspirations/llm-training.md`
