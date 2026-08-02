# État de l'art — Pré-training LLM/SLM

> Dernière mise à jour : 2026-08-02 · Maintenu par la skill `inventor-lab`
> **Fiche vivante** : mise à jour *en place* à chaque source pertinente. On révise, on n'empile pas.
> Contraintes du contexte : CPU only, mémoire réduite (cf. `../00_research_notes.md`).

## En bref
Sous contrainte CPU/mémoire, le pré-training « from scratch » n'est réaliste que pour des
**petits modèles jouets** (gpt2-style) — utile pour comprendre, prototyper, former. Les
modèles frontière (Inkling-Small, MoE entraîné sur GB300) sont ici des **références de
l'état de l'art**, pas des cibles reproductibles localement. L'intérêt opérationnel se
situe donc côté SLM jouets + recettes, la donnée synthétique jouant un rôle croissant.

## Techniques / approches clés
_Format : technique — statut — quand l'utiliser — source(s)._

- **Pipeline complet local pretrain → SFT → DPO (LLM Builder)** — établi (outil) — apprendre/POC, entraîner un gpt2-style de bout en bout sur son propre corpus, voir la loss chuter en direct. — `../Inspirations/llm-training.md`
- **SLM from scratch (notebooks Colab / repos)** — établi — construire un petit LM pas à pas, base pédagogique et démo de formation. — `../Inspirations/llm-training.md`
- **Pré-training MoE frontière (Inkling-Small, 276B/12B actifs)** — SOTA, **hors contrainte** — référence : « petit qui égale grand » via post-training, mais pré-entraîné sur GB300. — `../Inspirations/llm-training.md`
- **Données synthétiques intégrées au pipeline** — émergent — voir la fiche sœur `synthetic_data.md`. — `../Inspirations/llm.md`

## Ce qui a bougé récemment
- [2026-08-02] Première population depuis `Inspirations/`. Distinction posée entre ce qui est *reproductible localement* (SLM jouets via LLM Builder / notebooks) et ce qui reste *référence SOTA* (Inkling-Small).

## Questions ouvertes / à trancher
- Quel est le plus petit modèle jouet utile pour une démo/POC « bibliothèque » entraînable en CPU raisonnable ? (→ `../OpenQuestions/`)

## Candidats d'expériences
- `../Experiments/exp_slm_from_scratch_biblio.md`

## Sources dans la base
- **LLM from scratch** (LLM Builder, notebooks) — `../Inspirations/llm-training.md`
- **Inkling-Small** (pré-training MoE de référence) — `../Inspirations/llm-training.md`
- **SLM pre-training pipeline** (synthèse, inclut données synthétiques) — `../Inspirations/llm.md`
