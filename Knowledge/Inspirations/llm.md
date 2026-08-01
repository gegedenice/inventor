# LLM/SLM/VLM architecture

## VLM trained without image encoder

Why is it interesting?

### Resources

- https://huggingface.co/spaces/HuggingFaceM4/encoder-free-vlm#trying-an-even-bigger-decoder-and-different-data

### Takeaway

### Questions

- Can the approach (manage VLM embeddings layer) be generalized to LLM to suit other goals (introduce knowledge, encode skills..)?

---

## World models

Why is it interesting?
- Predicting state rather than token

### Resources

- Nvidia Cosmo3 models (open platform of World Foundation Models (WFMs), training recipes, optimization practices, and data curation tools — purpose-built to understand, 
  simulate, and reason about the physical world for robotics, autonomous vehicles, and physical AI.): 
  for example https://huggingface.co/nvidia/Cosmos3-Nano 
- Qwen Agent world (paper+model+benchmark): https://huggingface.co/collections/Qwen/qwen-agentworld
- Jepa world models (Meta): https://github.com/facebookresearch/jepa-wms

### Takeaway

- [Cosmo3 models] "Built on a Mixture-of-Transformers (MoT) architecture, a single model pairs an autoregressive reasoner with a diffusion generator — reasoning before it generates — to unify vision reasoning, world generation, and action prediction across text, image, video, ambient sound, and action in one forward pass."
- [Qwen Agente world model]: "A world model predicts environment dynamics based on current observations and actions, serving as a core cognitive mechanism for reasoning and planning. 
  In this work, we investigate how world modeling based on language models can further push the boundaries of general agents"

### Questions

- Can world models (with concept of states) be generalized/applied to librarians workflows? 
- or for agent memory management?

### Random Connections

---

## SLM pre-training pipeline

Why is it interesting?
- Very complete synthesis
- including synthtic data generation

### Resources

- https://drive.google.com/file/d/1yWmT2Jz_6kn5xcZVOl_gzXVqPoE-vnlY/view

### Takeaway


### Questions


### Random Connections

---

## SLM extraction de propositions atomiques

### Resources

- SLM: https://huggingface.co/Zual/MPropositionneur-V2
- Paper: https://hal.science/hal-05597666v1

### Takeaway

"Qu'est-ce qu'une proposition atomique ? La notion est partout en NLP (FActScore, Dense X retrieval...), mais sans définition formelle consensuelle.
Dans notre article accepté à CORIA (CORIA-TALN 2026), nous comblons ce vide en deux temps :
- Une base formelle : En nous appuyant sur la théorie de l'information sémantique de Carnap et Bar-Hillel (1953), nous définissons ce qui est sécable ou non dans la sémantique d'un texte.
- Un modèle frugal (MPropositionneur-V2) : distillé de Qwen3-72B vers 0.6B. Il surpasse l'état de l'art (Chen et al. 2024) avec un format plus léger et multilingue.
Les résultats ? Des performances améliorées en extraction de triplets, en recherche d'information et en évaluation de résumé."

### Questions

- Index creation on multiples docs?
- Synthetize publications/thesis abstracts?

### Random Connections

- Karpathy LLM-wiki

---

## HuggingFace ecosystem

Why is it interesting?
- Remote inference or fine-tuning
- Agentic management (hf cli no installation `uvx hf...`, `hf skills add`...)

### Resources

- Jobs serving with hf cli (temporary endpoints): https://huggingface.co/docs/hub/jobs-serving
- Permanent endpoints: https://huggingface.co/docs/inference-endpoints/index
- Agent local with PI and llama.cpp (can be replaced by jobs serving or endpoint): https://huggingface.co/docs/hub/agents-local
- hf-mount: https://github.com/huggingface/hf-mount

### Takeaway


### Questions


### Random Connections

---

## AirLLM — inférence couche par couche (modèle géant sur GPU minuscule)

AirLLM exécute un très grand transformer en le découpant en couches : charger une couche depuis le disque, calculer, garder l'activation, libérer la couche, passer à la suivante — sans quantization, distillation ni pruning (70B sur 4 Go, 405B sur 8 Go). L'article gopubby l'applique à Kimi K3 (2.8T paramètres) sur un seul GPU 4 Go.

Why is it interesting?
- Renverse l'hypothèse « faire tenir tout le modèle en VRAM » → « faire tenir une seule couche à la fois » : un transformer est une pile séquentielle, une couche suffit pour traiter un token. Pic VRAM réduit de >95% sans perte de fidélité.
- Transforme le goulot mémoire en goulot d'**E/S disque** : chaque forward relit tout le modèle depuis le disque → 5–30× plus lent selon la vitesse disque. Compromis assumé : latence contre capacité.
- Détails réutilisables : shards de couches sauvés séparément (`layer_shards_saving_path`), *prefetching* qui recouvre chargement et calcul, `delete_original` pour économiser le disque, compression block-wise (poids seuls) pour ~3× de gain.
- Frugalité radicale : servir un modèle frontière sur du matériel de récupération (souveraineté, coût, hors-ligne) — argument fort en bibliothèque.

### Resources

- https://ai.gopubby.com/unbelievable-run-kimi-k3-2-8-trillion-parameters-on-a-single-4gb-gpu-23590e7a16c2 (article Kimi K3, page rendue en JS)
- Repo AirLLM : https://github.com/lyogavin/airllm
- Explainers : https://explainx.ai/blog/airllm-run-70b-llm-4gb-gpu-inference-2026

### Takeaway

"AirLLM optimizes inference memory usage, allowing 70B large language models to run inference on a single 4GB GPU card without quantization, distillation and pruning. And you can run 405B Llama3.1 on 8GB vram now."

### Questions

- Idée utilisateur : pointer `layer_shards_saving_path` vers un stockage objet distant (HF buckets / hf-mount) → séparer stockage (illimité, partagé, versionné) et compute (n'importe quel petit nœud) ? Le prefetch masque-t-il la latence réseau ?
- Pour un MoE (Kimi K3, Inkling-Small) : ne streamer que les experts réellement routés par token plutôt que toutes les couches — l'E/S s'effondre-t-elle ?
- Même bascule « un à la fois » appliquée au KV-cache long contexte (cf. Memory Caching, medium_llm-rnn.md) plutôt qu'aux poids ?

### Random Connections

- HuggingFace moonbot (agentic.md) et HuggingFace ecosystem + hf-mount (ce fichier) : brique de stockage pour l'idée « shards de couches en HF buckets ».
- Inkling-Small MoE (llm-training.md) : le streaming sélectif d'experts est le mariage naturel MoE × AirLLM.
- Memory Caching RNN (Papers/medium_llm-rnn.md) : streamer le KV-cache comme AirLLM streame les couches.
- Idée « fusion de logits » (Ideas/ideas_2026-07-31.md) : AirLLM rend plausible de charger séquentiellement plusieurs modèles sur un même petit GPU.

---

## Colibri — runtime MoE frugal : experts streamés du disque (« JIT pour les poids »)

Moteur en C pur, zéro dépendance (Apache-2.0, ~20k étoiles) qui fait tourner GLM-5.2 (744B MoE) sur une machine grand public à ~25 Go de RAM en streamant les experts depuis le disque. Traite VRAM / RAM / NVMe comme une seule hiérarchie mémoire ; ne charge que les ~40B paramètres actifs par token (~11B changent d'un token à l'autre).

Why is it interesting?
- Va plus loin qu'AirLLM : ne streame pas *toutes* les couches mais seulement les **experts routés** (sparsité MoE) — c'est exactement l'idée « MoE × AirLLM » de la passe 2, réalisée et mesurée.
- « JIT pour les poids » : les paramètres ne sont pas un état résident mais des *données mises en scène* (VRAM/RAM/NVMe) quand le routeur prouve qu'on en a besoin ; un cache d'apprentissage épingle les experts chauds → le moteur s'accélère à l'usage.
- Placement ≠ précision : le placement ne décide que la *vitesse* ; les décisions du routeur et la précision des poids sont identiques que l'expert réponde depuis la VRAM ou le disque. Prefetch un layer en avance (routing 71,6 % prévisible).
- CPU-only viable : pur C, pas de GPU requis (128 Go CPU ~1,8 tok/s ; 25 Go ~0,05–0,1 tok/s à froid). Argument frugalité/souveraineté : « tenir » un modèle frontière sur du matériel possédé, pas le louer derrière une API.
- Dashboard « Atlas / Brain » : 19 456 experts en cortex vivant ; galaxie 3-D où la position = affinité de routage *mesurée*, pas un embedding appris (13 260 experts caractérisés, 1 041 spécialistes répliqués groupés par sujet : poésie, droit, chinois, SQL…).

### Resources

- https://github.com/JustVugg/colibri
- Site + dashboard : https://justvugg.github.io/colibri
- Container GLM-5.2 int4 (HF) : https://huggingface.co/mastouri/GLM-5.2-colibri-int4-g64-with-int8-mtp

### Takeaway

"Think of the core algorithm as a JIT, but for weights. (…) parameters are not resident state to be held, they are data to be staged across a heterogeneous storage hierarchy (VRAM / RAM / NVMe), exactly when the router proves they are needed."

### Questions

- Déploiement CPU-only en bibliothèque : le débit (~0,05–1,8 tok/s selon la RAM) suffit-il pour des tâches batch/asynchrones (enrichissement de notices la nuit) sur matériel possédé, sans GPU ni API ?
- Le dashboard Atlas (carte par affinité de routage *mesurée*, pas embedding) : transposable pour cartographier un fonds documentaire ou le *comportement* d'un agent bibliothécaire (quels outils/« experts » s'activent sur quels sujets) ?
- Le cache d'apprentissage qui épingle les experts chauds selon *votre* usage : transférable à un préchargement adaptatif de ressources documentaires (collections chaudes) ?

### Random Connections

- AirLLM (ce fichier) : Colibri est la version MoE-aware et mesurée du streaming disque — il réalise l'idée « MoE × AirLLM » (Ideas/ideas_2026-08-01, passe 2).
- Inkling-Small (llm-training.md) / Kimi K3 (agentic.md) : MoE creux — cibles naturelles du staging d'experts.
- Dataviz DeckGL (dataviz.md) : l'Atlas 3-D est un réseau sémantique de comportement *mesuré* — même famille « carte haute densité ».
- Graphify (kb.md) : « position = affinité mesurée, pas embedding appris » ↔ « la topologie du graphe EST la similarité ».
