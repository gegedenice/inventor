# État de l'art — Post-training LLM/SLM

> Dernière mise à jour : 2026-08-05 · Maintenu par la skill `inventor-lab`
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
- **Orchestration de fine-tuning one-command (Soup)** — émergent — SFT/DPO/GRPO/PPO/KTO…, QLoRA local (7B/8 Go), quantization auto, pile d'efficacité mémoire (activation offloading, GaLore, DeepSpeed/FSDP), export GGUF ; « streaming RAM/VRAM » côté entraînement. — `../Inspirations/llm-training.md`
- **Garde-fou RL : reward-hacking auto-mitigation en boucle fermée (Soup)** — émergent — détecter le reward hacking en cours de RLHF, relever la KL, rollback au dernier checkpoint sain. — `../Inspirations/llm-training.md`
- **Steering / modification de comportement sans fine-tuning** — émergent — infléchir un LLM à très bas coût, réversible. — `../Papers/iaetbibliotheques_steering.md`
- **Alignement SFT + DPO (pipeline local)** — établi — aligner un petit modèle après pré-training (LLM Builder). — `../Inspirations/llm-training.md`
- **Effort de raisonnement variable via RL (Inkling-Small)** — émergent — exposer un curseur coût/qualité sur un même modèle. — `../Inspirations/llm-training.md`
- **QAT natif basse précision (Kimi K3, post-training en FP4)** — émergent — exécuter le post-training *nativement* en 4-bit (poids FP4, activations 8-bit) : le modèle s'adapte aux numériques bas, évitant la perte d'accuracy d'une quantization a posteriori. Offsette la « taxe d'échelle » mémoire. — `../Papers/kimi3_architecture_efficiency.md`

## Ce qui a bougé récemment
- [2026-08-05] Ingest **Soup** : le fine-tuning se banalise (« un YAML, une commande ») et intègre une pile d'*offloading mémoire* — le pendant entraînement du streaming d'inférence. Notable : garde-fou anti reward-hacking en boucle fermée (détecte→corrige→continue).
- [2026-08-03] Ingest **Kimi K3** : le **QAT natif FP4** entre dans la fiche — entraîner *dans* la précision de déploiement plutôt que quantizer après coup. Piste directe pour un SLM biblio déployable CPU sans perte (cf. Colibri int4, candidat Experiments).
- [2026-08-02] Première population depuis `Inspirations/`. Le patron **on-policy distillation** (Inkling-Small) recoupe et confirme la distillation 72B→0.6B de MPropositionneur : la voie « petit spécialisé distillé d'un grand » se dessine comme la plus prometteuse pour un contexte frugal.

## Questions ouvertes / à trancher
- On-policy distillation + RL : reproductible frugalement pour distiller un SLM « bibliothécaire » depuis un grand modèle ? (→ `../OpenQuestions/`)
- Steering vs fine-tuning : quels comportements sont mieux servis par l'un que par l'autre ?

## Candidats d'expériences
- `../Experiments/exp_distill_slm_bibliothecaire.md`
- `../Experiments/exp_qat_fp4_slm_biblio.md`
- `../Experiments/exp_soup_finetune_slm_biblio.md`

## Sources dans la base
- **Inkling-Small** (on-policy distillation + RL, effort variable) — `../Inspirations/llm-training.md`
- **SLM extraction de propositions atomiques / MPropositionneur** (distillation 72B→0.6B) — `../Inspirations/llm.md`
- **Colab CLI** (fine-tuning QLoRA délégué) — `../Inspirations/agentic.md`
- **Le Steering** — `../Papers/iaetbibliotheques_steering.md`
- **LLM from scratch** (SFT + DPO local) — `../Inspirations/llm-training.md`
- **Soup** (fine-tuning/post-training one-command, offloading mémoire, reward-hacking mitigation) — `../Inspirations/llm-training.md`
- **Kimi K3 architecture** (QAT natif FP4) — `../Papers/kimi3_architecture_efficiency.md`
