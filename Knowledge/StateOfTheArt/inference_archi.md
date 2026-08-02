# État de l'art — Optimisation de l'inférence & architectures

> Dernière mise à jour : 2026-08-02 · Maintenu par la skill `inventor-lab`
> **Fiche vivante** : mise à jour *en place* à chaque source pertinente. On révise, on n'empile pas.
> Contraintes du contexte : CPU only, mémoire réduite (cf. `../00_research_notes.md`).

## En bref
C'est le domaine le plus mûr de la base. Une même bascule y revient : **ne pas faire tenir
tout le modèle en mémoire, mais mettre en scène ce dont on a besoin, quand on en a besoin.**
Elle se décline en streaming disque des poids (AirLLM), streaming des *experts routés* d'un
MoE (Colibri, viable CPU-only), architectures encoder-free et MoE à effort variable
(Inkling-Small). Le compromis assumé : latence/débit contre capacité — donc pertinent pour
des tâches **batch/asynchrones** sur matériel possédé, pas pour de l'interactif.

## Techniques / approches clés
_Format : technique — statut — quand l'utiliser — source(s)._

- **Inférence couche-par-couche (AirLLM)** — établi — modèle géant sur petit GPU, batch tolérant à la latence ; goulot = E/S disque (70B/4 Go, 405B/8 Go, sans quantization). — `../Inspirations/llm.md`
- **Streaming d'experts MoE, « JIT for weights » (Colibri)** — émergent — MoE géant sur RAM modeste, **CPU-only viable** (GLM-5.2 744B ~25 Go RAM ; ~1,8 tok/s @128 Go) ; ne streame que les experts routés. — `../Inspirations/llm.md`
- **Architectures encoder-free multimodal** — émergent — éviter un pipeline d'encodeur lourd (VLM sans encodeur ; Inkling-Small : audio dMel + patches image). — `../Inspirations/llm.md`, `../Inspirations/llm-training.md`
- **MoE à effort de raisonnement variable (Inkling-Small)** — émergent — balayer une courbe coût/perf avec un seul modèle (minimal→xhigh). — `../Inspirations/llm-training.md`
- **Mémoire croissante pour RNN (Memory Caching)** — émergent — compromis entre récurrence efficace et attention pleine coûteuse. — `../Papers/medium_llm-rnn.md`
- **Fusion de logits multi-modèles (fused tiny local LLMs)** — émergent — combiner plusieurs petits modèles locaux au niveau des logits. — `../Papers/medium_fused-tiny-local-llms.md`
- **World models (prédire l'état plutôt que le token)** — exploratoire — Cosmos, Qwen AgentWorld, JEPA. — `../Inspirations/llm.md`
- **Infra d'inférence/stockage (HF jobs serving, endpoints, hf-mount)** — établi — servir/stocker à distance, séparer compute et stockage. — `../Inspirations/llm.md`

## Ce qui a bougé récemment
- [2026-08-02] Première population depuis `Inspirations/`. Deux briques fortes entrées : **AirLLM** (streaming disque des couches) et **Colibri** (streaming d'experts MoE, CPU-only) — elles réalisent et *mesurent* l'idée « MoE × AirLLM » des passes d'idées.

## Questions ouvertes / à trancher
- Le débit CPU-only de Colibri (~0,05–1,8 tok/s selon RAM) suffit-il pour de l'enrichissement de notices nocturne sur matériel possédé ? (→ `../OpenQuestions/`)
- Le prefetch d'AirLLM masque-t-il la latence réseau si `layer_shards_saving_path` pointe vers hf-mount/buckets ? (séparer stockage illimité/versionné et compute)

## Candidats d'expériences
- `../Experiments/exp_colibri_notices_nocturne.md`
- `../Experiments/exp_airllm_shards_distants.md`

## Sources dans la base
- **AirLLM**, **Colibri**, **VLM sans encodeur**, **World models**, **HuggingFace ecosystem** — `../Inspirations/llm.md`
- **Inkling-Small** (MoE, encoder-free, effort variable) — `../Inspirations/llm-training.md`
- **Fused tiny local LLMs** — `../Papers/medium_fused-tiny-local-llms.md`
- **Memory Caching (RNN)** — `../Papers/medium_llm-rnn.md`
- _À ingérer :_ Liquid LFM2 encoders (causal decoder → bidirectional encoder) — https://www.liquid.ai/blog/lfm2-5-encoders
