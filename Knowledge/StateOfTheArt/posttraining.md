# État de l'art — Post-training LLM/SLM

> Dernière mise à jour : 2026-08-02 · Maintenu par la skill `inventor-lab`
> **Fiche vivante** : mise à jour *en place* à chaque source pertinente. On révise, on n'empile pas.
> Contraintes du contexte : CPU only, mémoire réduite (cf. `../00_research_notes.md`).

## En bref
Le terrain le plus **actionnable** sous contrainte : plutôt que de pré-entraîner, on part
d'un modèle existant et on l'adapte à moindre coût. Trois familles ressortent : la
**distillation grand→petit** (un petit spécialisé qui dépasse un grand sur une tâche ciblée),
le **fine-tuning délégué** (sans infra locale), et le **steering** (modifier le comportement
*sans* ré-entraînement). L'inférence du modèle final, elle, doit rester frugale.

## Techniques / approches clés
_Format : technique — statut — quand l'utiliser — source(s)._

- **On-policy distillation (teacher→student) + RL agentique (Inkling-Small)** — émergent — faire un petit qui dépasse le grand en raisonnement/agent sur une tâche ciblée (le grand garde l'avantage en factualité). — `../Inspirations/llm-training.md`
- **Distillation frugale grand→petit (MPropositionneur, 72B→0.6B)** — établi/émergent — obtenir un SLM spécialisé, multilingue, frugal, supérieur à l'état de l'art antérieur. — `../Inspirations/llm.md`
- **Fine-tuning délégué (Colab CLI QLoRA/TRL, Tinker, HF jobs)** — établi — fine-tuner sans infra locale, depuis un prompt d'agent. — `../Inspirations/agentic.md`, `../Inspirations/llm.md`, `../Inspirations/llm-training.md`
- **Steering / modification de comportement sans fine-tuning** — émergent — infléchir un LLM à très bas coût, réversible. — `../Papers/iaetbibliotheques_steering.md`
- **Alignement SFT + DPO (pipeline local)** — établi — aligner un petit modèle après pré-training (LLM Builder). — `../Inspirations/llm-training.md`
- **Effort de raisonnement variable via RL (Inkling-Small)** — émergent — exposer un curseur coût/qualité sur un même modèle. — `../Inspirations/llm-training.md`

## Ce qui a bougé récemment
- [2026-08-02] Première population depuis `Inspirations/`. Le patron **on-policy distillation** (Inkling-Small) recoupe et confirme la distillation 72B→0.6B de MPropositionneur : la voie « petit spécialisé distillé d'un grand » se dessine comme la plus prometteuse pour un contexte frugal.

## Questions ouvertes / à trancher
- On-policy distillation + RL : reproductible frugalement pour distiller un SLM « bibliothécaire » depuis un grand modèle ? (→ `../OpenQuestions/`)
- Steering vs fine-tuning : quels comportements sont mieux servis par l'un que par l'autre ?

## Candidats d'expériences
- `../Experiments/exp_distill_slm_bibliothecaire.md`

## Sources dans la base
- **Inkling-Small** (on-policy distillation + RL, effort variable) — `../Inspirations/llm-training.md`
- **SLM extraction de propositions atomiques / MPropositionneur** (distillation 72B→0.6B) — `../Inspirations/llm.md`
- **Colab CLI** (fine-tuning QLoRA délégué) — `../Inspirations/agentic.md`
- **Le Steering** — `../Papers/iaetbibliotheques_steering.md`
- **LLM from scratch** (SFT + DPO local) — `../Inspirations/llm-training.md`
