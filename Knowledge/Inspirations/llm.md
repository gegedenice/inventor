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
- Compression de contexte lisible↔model-native (kb.md, Nano-Capsulator/BabelTele) : les propositions atomiques en sont la variante *lisible et structurée* — cf. `../Syntheses/synthese_spectre_lisible_modelnative.md`

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

---

## Kimi K3 — masterclass d'efficacité (LatentMoE, KDA+MLA, QAT FP4, NoPE, AttnRes)

Analyse d'ingénierie du Kimi K3 de Moonshot (MoE multimodal, 2,8 T total / ~104 B actifs, 16/896 experts, contexte 1 M) : une pile de choix qui rendent un modèle géant *déployable*. Fulltext gardé en base.

Why is it interesting?
- **Stable LatentMoE** : compresser les tokens dans un espace latent réduit (hidden down-projeté à 3 584 dims) *avant* le routage → moins de mémoire d'activation et de trafic inter-nœuds. « Pratiquement obligatoire » au-delà de 1 T params. (bâtit sur Nemotron 3.)
- **Kimi Delta Attention (KDA)** : remplace l'attention standard par un *mécanisme linéaire à état de taille fixe mis à jour par token* → le KV-cache ne croît plus avec la longueur. Mixé à **MLA** (DeepSeek, KV compressé en un vecteur latent) en ratio **3:1** (3 KDA rapides + 1 Gated MLA pour la récupération exacte).
- **QAT natif FP4** : pipeline de *post-training exécuté nativement en FP4* (poids 4-bit, activations 8-bit) → le modèle s'adapte à la basse précision, évitant la perte d'accuracy. (Unsloth : jusqu'à 1–2 bit GGUF, 1,56 To → ~594 Go @ ~79 % top-1.)
- **AttnRes** (résidus par attention apprise entre couches, vs addition fixe) et **NoPE** (pas d'embedding positionnel ; index de séquence implicite) : préservent l'accuracy sous forte sparsité/quantization et étendent le contexte à 1 M.

### Resources

- Document fourni par l'utilisateur — fulltext in @../Papers/kimi3_architecture_efficiency.md
- Contexte : Kimi K3 déjà cité dans AgentENV (agentic.md) et AirLLM (ce fichier).

### Takeaway

"Kimi Delta Attention (KDA), used in Kimi K3, replaces standard attention with a linear mechanism that keeps a fixed-size state that updates per token. This brings the computational complexity down and stops the KV cache from growing with sequence length."

### Questions

- L'état de taille fixe de KDA (mis à jour par token) est-il un *latent steerable* : injecter un vecteur dans cet état pour biaiser le comportement sur tout un long document, à coût constant ?
- L'espace latent compressé de LatentMoE/MLA (3 584 dims) est-il un meilleur logement pour des vecteurs de steering (moins cher, plus robuste) que l'espace résiduel plein ?
- QAT natif basse précision : distiller/fine-tuner un SLM bibliothécaire *directement* en 4-bit pour un déploiement CPU sans perte ?

### Random Connections

- Memory Caching RNN (`../Papers/medium_llm-rnn.md`) : KDA est l'instance production de « état récurrent de taille fixe vs attention pleine ».
- Colibri / AirLLM (ce fichier) : Kimi K3 (2,8 T) est justement le modèle que Colibri/AirLLM cherchent à servir frugalement ; QAT FP4 réduit encore l'empreinte.
- Le Steering (`../Papers/iaetbibliotheques_steering.md`) + idée « vecteurs de steering comme compétences » (Ideas/2026-07-31) : cible naturelle = l'état KDA / le latent MLA.
- Inkling-Small (`llm-training.md`) : autre MoE frugal ; NoPE/AttnRes = autres « préserveurs d'accuracy ».

---

## kimi-k3-in-c — Kimi K3 (2,78 T) en C99 sur un seul CPU, 8,24 Go RAM

Moteur d'inférence portable en C99 (FareedKhan-dev) qui fait tourner Kimi K3 (2,78 T params) sur **un seul CPU en 8,24 Go de RAM — sans BLAS, sans framework, sans GPU**. Binaire de 176 Ko, doublé d'un tutoriel « build every box from scratch ».

Why is it interesting?
- **Union frugale d'AirLLM et de Colibri** : partie dense résidente en RAM + 82 432 experts « endormis sur disque », seuls 16/896 réveillés par token (~3,7 % actifs). MXFP4 : on multiplie directement depuis les nibbles, **jamais de déquantization** (économise mémoire *et* calcul).
- Implémente l'architecture Kimi K3 (cf. `Papers/kimi3_architecture_efficiency.md`) en kernels C lisibles : RMSNorm, **KDA** (69/93 couches), **MLA** (globale, 1 sur 4), **LatentMoE**, MXFP4 matmul.
- Compromis assumé et mesuré : ~**32,7 s/token** (8 tokens en 261 s) — c'est la *capacité*, pas la vitesse ; AVX2+FMA suffisent ; sortie déterministe cross-OS. Le mur est l'E/S/capacité, pas le CPU.
- Pédagogique et souverain : 176 Ko de binaire pour un modèle de 1,56 To, auditable, hors-ligne.

### Resources

- https://github.com/FareedKhan-dev/kimi-k3-in-c

### Takeaway

"only 16 of its 896 experts per layer fire for any given token and the rest sit asleep on disk. Keep the always-on part in memory, stream the sleeping experts, and it fits in 8.24 gigabytes on one CPU with no GPU."

### Questions

- **Réplicable pour des modèles mid-size ?** Pour un MoE moyen (Inkling-Small 276B/12B, ou 30–100B), la partie résidente est minuscule et les experts streamés bien moins nombreux → tok/s *bien* supérieur aux ~33 s/token de K3. Pour un *dense* mid-size, seul le streaming de couches (AirLLM) s'applique (pas de sparsité d'experts à exploiter). À mesurer.
- **Stocker les experts sur un HF bucket plutôt qu'en local ?** Personne ne garde 1,56 To en local — pointer le stockage des experts vers un montage distant (hf-mount / HF buckets) rendrait le modèle *empruntable* : compute local minuscule, poids distants mutualisés et versionnés. Reste le mur d'E/S réseau (le prefetch le masque-t-il pour du batch ?). Cf. idée « AirLLM × HF-buckets » et `../Experiments/exp_airllm_shards_distants.md`.
- MXFP4 « jamais déquantizé » : transposable à un runtime CPU biblio pour un SLM/MoE 4-bit natif (cf. QAT FP4 de Kimi K3) ?
- 176 Ko en C99, zéro dépendance : socle d'inférence souverain/hors-ligne pour un établissement (auditable, RGPD) ?

### Random Connections

- AirLLM + Colibri (ce fichier) : kimi-k3-in-c *est* l'union des deux — streaming disque des poids (AirLLM) + streaming des experts MoE routés « JIT for weights » (Colibri), en C99 mono-fichier. Colibri = moteur C pour GLM-5.2 ; celui-ci = moteur C pour Kimi K3.
- Kimi K3 architecture (`Papers/kimi3_architecture_efficiency.md`) : implémentation concrète de KDA/MLA/LatentMoE + MXFP4.
- Idée « MoE × AirLLM : ne streamer que les experts routés » (`Ideas/ideas_2026-08-01.md`, passe 2) : réalisée et mesurée ici.
- Les deux questions utilisateur (mid-size ? poids sur HF bucket ?) sont cadrées en expériences : `../Experiments/exp_moe_streaming_midsize.md` (généralisation MoE mid-size) et `../Experiments/exp_airllm_shards_distants.md` (poids streamés depuis un stockage objet distant).
- LLM from scratch (`llm-training.md`) : même veine « construire chaque brique soi-même » pour comprendre.

---

## Needle 2 — modèle 45M en 14 Mo / 28 Mo RAM : tool-calling + extraction contraints par grammaire

Modèle ouvert (Cactus Compute, MIT) de 45 M params pour l'appel d'outils, l'usage d'appareil et l'extraction structurée : **un seul binaire de 14 Mo qui tient une session complète dans ~28 Mo de RAM**, inférence 100 % locale (aucun réseau). Architecture « Simple Attention Network », quantifié **CQ2-bit** (Cactus Quants), moteur maison. `pip install cactus-needle`. arXiv:2607.18363.

Why is it interesting?
- **Extraction = appel d'outil avec un seul outil** : on déclare le schéma du *record* comme unique outil et la grammaire byte-level n'admet qu'un appel de ce nom → **conformité au schéma garantie, pas demandée**. « text in, JSON out » : transposable direct à l'extraction de champs de notice (facture/CV/contrat → notice, métadonnées).
- **Décodage contraint par une grammaire compilée depuis le schéma** : ranges, patterns, longueurs, énumérations (`Literal`, `Field`) deviennent des contraintes de décodage → le modèle *ne peut pas* émettre une valeur hors-schéma. Le `reasoning` (dérivation `'ten minutes' -> minutes 10`) reste libre et lisible, seule la sortie est contrainte.
- **Confidence-gated escalation** : chaque réponse porte un score calibré (min de deux signaux : tête post-hoc + proba de décodage) ; « act above threshold, escalate below ». Motif idéal pour l'enrichissement nocturne : le petit modèle traite tout, escalade au gros seulement le douteux. Requête hors-sujet → appel vide `[]`, jamais de free-text inventé.
- **Mémoire bornée ~28 Mo quelle que soit la longueur** : fenêtre glissante de 256 tokens + outils épinglés comme *KV sinks*. Souverain, hors-ligne, auditable — colle aux contraintes CPU/mémoire réduite de la base.
- **Frontière taille/qualité repoussée vers le bas** : 5× à 70× plus petit que FunctionGemma 270M / LFM2.5 230M / Apple FM, 2 bits contre leur f16. **Tool retrieval** intégré (tête contrastive, top-5 outils/tour, index persistable) pour de gros catalogues. Fine-tuning LoRA fusionné à l'export → toujours un seul `.cact`.
- **Architecture « Simple Attention Network » (recette dense pour petit modèle)** : le FFN est remplacé par un **Hadamard MLP** — transformée de Walsh-Hadamard fixe, orthonormale, appliquée en *n·log n* **sans poids à lire** ; attention **GQA** ; **mémoire clé-valeur « engram »** (lignes tirées de tables n-gram hachées, tirant à deux couches) ; **hyper-connexions multi-voies** ; routage doublement stochastique par itération de **Sinkhorn**. Chaque bloc porte sa règle de mise à jour, avec gates apprises et dépendantes de l'entrée. Réservoir de briques concrètes à voler pour un SLM biblio maison.
- **Packaging : le *modèle* est un package pip auto-suffisant** — `pip install cactus-needle`, moteur d'inférence 14 Mo **baké dans le package**, poids **fetchés une fois depuis Hugging Face puis cachés**, « nothing else to build », zéro I/O réseau à l'inférence. Un modèle de fondation qui s'installe **comme une bibliothèque** : pas de fichiers de poids à gérer, pas de runtime à compiler. Patron de distribution souverain/reproductible pour livrer un SLM clé-en-main à un établissement.

### Resources

- https://github.com/cactus-compute/needle
- https://huggingface.co/Cactus-Compute/needle2
- https://arxiv.org/abs/2607.18363 (Simple Attention Network : Hadamard MLP, GQA, engram KV, hyper-connections)

### Takeaway

"The whole model is a single 14MB binary that runs a full session in about 28MB of RAM. [...] a byte-level grammar compiled from your schemas constrains every token."

"weights baked into a single 14MB engine; no separate model files to manage, and inference does no network. [...] The inference engine is fetched once from Hugging Face and cached; there is nothing else to build."

### Questions

- **Grammaire byte-level depuis un profil de notice** : peut-on compiler la grammaire depuis un schéma MARC/Unimarc/EAD pour garantir qu'une extraction reste *conforme au format catalographique* (jamais de champ hors-schéma) ? Cf. `../Experiments/exp_needle_extraction_notices.md`.
- **Escalade confidence-gated sur un fonds réel** : quel taux d'escalade (petit modèle → gros) sur un corpus biblio océrisé ? Le seuil calibré rend-il le batch nocturne CPU viable sans relire tout au gros modèle ?
- **SLM d'extraction souverain sur CPU** : 14 Mo / 28 Mo RAM = brique d'extraction embarquable sur matériel modeste d'établissement (RGPD, hors-ligne). Le LoRA sur nos propres schémas suffit-il à égaler un gros LLM sur la tâche « texte → champs » ?
- **Distribuer un SLM biblio comme un package pip** : peut-on empaqueter notre propre SLM (moteur baké + poids fetchés/cachés depuis un HF bucket d'établissement) pour une installation « une commande, zéro build, hors-ligne ensuite » ? Renverse l'hypothèse « un modèle = des fichiers de poids + un runtime à assembler ». Cf. `HuggingFace ecosystem` (ce fichier).

### Random Connections

- kimi-k3-in-c, Colibri, AirLLM (ce fichier) : même famille « souverain/frugal », mais Needle vise le **bas extrême** (14 Mo / 28 Mo) là où eux font tenir le *géant* sur petit matériel. Complémentaires sur l'axe frugalité.
- SLM extraction de propositions atomiques (ce fichier) : même finalité « structurer du texte », ici par **grammaire + confidence** au lieu d'un pipeline de propositions.
- ContextGem (`kb.md`) : **pôle opposé du spectre extraction** — gros LLM long-contexte avec justifications+références vs Needle petit/grammaire/on-device/confidence. Deux stratégies pour « texte → JSON structuré », à arbitrer selon coût/traçabilité.
- Compression de contexte (Nano-Capsulator, BabelTele, `kb.md`) : Needle *borne* la mémoire (256 tokens + KV sinks) au lieu de *compresser* le contexte — deux réponses au coût du contexte.
- Distillation SLM bibliothécaire + QAT FP4 (`../Experiments/exp_distill_slm_bibliothecaire.md`, `exp_qat_fp4_slm_biblio.md`) : Needle = preuve qu'un SLM quantifié natif (CQ2-bit) fait le travail d'extraction/outils.
- HuggingFace ecosystem (ce fichier) : le moteur et les poids de Needle sont *fetchés une fois puis cachés* depuis HF — même brique hf-mount/serving, ici au service d'un **modèle-package** auto-suffisant plutôt que de shards de poids streamés (cf. AirLLM, `exp_airllm_shards_distants.md`).
