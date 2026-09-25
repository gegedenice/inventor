# État de l'art — Optimisation de l'inférence & architectures

> Dernière mise à jour : 2026-09-25 · Maintenu par la skill `inventor-lab`
> **Fiche vivante** : mise à jour *en place* à chaque source pertinente. On révise, on n'empile pas.
> Contraintes du contexte : CPU only, mémoire réduite (cf. `../00_research_notes.md`).

## En bref
C'est le domaine le plus mûr de la base. Une première bascule y revient : **ne pas faire tenir
tout le modèle en mémoire, mais mettre en scène ce dont on a besoin, quand on en a besoin** —
streaming disque des poids (AirLLM), streaming des *experts routés* d'un MoE (Colibri, viable
CPU-only), architectures encoder-free et MoE à effort variable (Inkling-Small). Trois axes
complémentaires s'y sont ajoutés depuis : **pooler la mémoire de plusieurs machines** (SwarmLLM,
P2P navigateur/WebRTC) ; **le modèle minuscule complet on-device** (Needle 2, 14 Mo) ; et la
**quantization data-oblivious** qui compresse KV-cache *et* vecteurs de recherche **sans
entraînement ni calibration** (TurboQuant). Deux fils unificateurs montent : (1) *data-oblivious /
« ne jamais déquantizer »* — ne pas apprendre la compression des données ; (2) *frugal localement,
escalader ou pooler au besoin* (Needle confidence-gate, SwarmLLM pool de postes). Compromis assumé :
latence/débit contre capacité — surtout pertinent pour des tâches **batch/asynchrones** sur
matériel possédé, l'interactif restant l'exception (Needle on-device, turbovec en recherche).

## Techniques / approches clés
_Format : technique — statut — quand l'utiliser — source(s)._

- **Inférence couche-par-couche (AirLLM)** — établi — modèle géant sur petit GPU, batch tolérant à la latence ; goulot = E/S disque (70B/4 Go, 405B/8 Go, sans quantization). — `../Inspirations/llm.md`
- **Streaming d'experts MoE, « JIT for weights » (Colibri)** — émergent — MoE géant sur RAM modeste, **CPU-only viable** (GLM-5.2 744B ~25 Go RAM ; ~1,8 tok/s @128 Go) ; ne streame que les experts routés. — `../Inspirations/llm.md`
- **Moteur MoE mono-fichier C99 (kimi-k3-in-c)** — émergent — dense résident + experts routés streamés du disque + MXFP4 *sans déquantization* ; Kimi K3 2,78 T sur 1 CPU / 8,24 Go, binaire 176 Ko, AVX2 (mais ~33 s/token : capacité, pas vitesse). — `../Inspirations/llm.md`
- **Architectures encoder-free multimodal** — émergent — éviter un pipeline d'encodeur lourd (VLM sans encodeur ; Inkling-Small : audio dMel + patches image). — `../Inspirations/llm.md`, `../Inspirations/llm-training.md`
- **MoE à effort de raisonnement variable (Inkling-Small)** — émergent — balayer une courbe coût/perf avec un seul modèle (minimal→xhigh). — `../Inspirations/llm-training.md`
- **Compression de contexte / représentations model-native (Nano-Capsulator, BabelTele)** — émergent — réduire tokens/latence en compressant le prompt/contexte (NL lisible transférable, ou non-lisible mais décodable : 99,5 % de sémantique à ~28 % du volume). — `../Inspirations/kb.md`, `../Papers/2606.19857v1.pdf`, `../Papers/2402.18700v2.pdf`
- **Mémoire croissante pour RNN (Memory Caching)** — émergent — compromis entre récurrence efficace et attention pleine coûteuse. — `../Papers/medium_llm-rnn.md`
- **Fusion de logits multi-modèles (fused tiny local LLMs)** — émergent — combiner plusieurs petits modèles locaux au niveau des logits. — `../Papers/medium_fused-tiny-local-llms.md`
- **World models (prédire l'état plutôt que le token)** — exploratoire — Cosmos, Qwen AgentWorld, JEPA. — `../Inspirations/llm.md`
- **Attention linéaire à état fixe (Kimi Delta Attention, KDA)** — émergent — long contexte à mémoire constante : remplace l'attention standard par un état de taille fixe mis à jour par token ; le KV-cache ne croît plus. Mixé à MLA en 3:1 (3 KDA + 1 Gated MLA pour la récupération exacte). — `../Papers/kimi3_architecture_efficiency.md`
- **Stable LatentMoE (routage en espace latent compressé)** — émergent — compresser les tokens (hidden→3 584 dims) avant de router aux experts : moins d'activation mémoire et de trafic inter-nœuds ; « obligatoire » au-delà de 1 T params. — `../Papers/kimi3_architecture_efficiency.md`
- **NoPE + Attention Residuals** — exploratoire — préserver l'accuracy sous forte sparsité/quantization et étendre le contexte (index de séquence implicite ; résidus par attention apprise). — `../Papers/kimi3_architecture_efficiency.md`
- **SLM on-device contraint par grammaire (Needle 2 / Simple Attention Network)** — émergent — tool-calling + extraction structurée en **14 Mo / ~28 Mo RAM**, décodage contraint par grammaire byte-level (conformité au schéma garantie), confidence-gated (escalade sous seuil), mémoire bornée (fenêtre 256 tokens + KV sinks) ; CQ2-bit, Hadamard MLP + engram KV + GQA + hyper-connections ; extraction = tool-calling avec un seul outil. — `../Inspirations/llm.md`
- **Inférence P2P layer-shardée dans le navigateur (SwarmLLM)** — émergent — **pooler la mémoire de plusieurs appareils** (portables/téléphones) plutôt que streamer d'un seul disque : chaque device tient une tranche de couches, activation 10 Ko sur WebRTC ; moteur WebGPU maison (~50 kernels WGSL) au memory-roofline (9→16 tok/s spéculatif sur GB10, > llama.cpp natif) ; MTP speculative decoding **bit-exact** ; goulot = prefill série (DeltaNet) + confidentialité des activations entre pairs. — `../Inspirations/llm.md`
- **Décision typée calibrée non-générative (System One / Jev, AnyJev, GLiNER2.5-Decide)** — émergent — **ne pas générer du tout** : lire en *un seul passage* une décision typée + une probabilité **calibrée seuillable** (distribution next-token sur tokens-labels, ou tête early-exit sur l'état caché). AnyJev L2 = tête closed-form à ~⅔ de la profondeur, **0,68× d'un forward**, CPU, sans entraînement ; GLiNER2.5-Decide = classifieur encodeur 340M (DeBERTa) CPU, labels à l'appel. Pour classer/router/scorer/gater dans un pipeline. Brique réutilisable = calibration + contrat de type + seuil d'auto-décision (l'archi, elle, reste de la classif encodeur). — `../Inspirations/llm.md`
- **Quantization data-oblivious (TurboQuant / QJL / PolarQuant ; turbovec)** — émergent — compresser KV-cache *et* vecteurs de recherche **sans phase d'entraînement ni calibration sur corpus** : rotation aléatoire → loi par-coordonnée connue → codebook Lloyd-Max calculé par les maths ; distorsion quasi-optimale, overhead des constantes de bloc éliminé (QJL = 1 bit de signe ; PolarQuant = radius+angles). KV-cache à 3 bits sans perte, jusqu'à 8× sur les logits d'attention (H100) ; côté recherche 16× de compression, kernels SIMD CPU > FAISS (turbovec). — `../Inspirations/kb.md`
- **Infra d'inférence/stockage (HF jobs serving, endpoints, hf-mount)** — établi — servir/stocker à distance, séparer compute et stockage. — `../Inspirations/llm.md`

## Ce qui a bougé récemment
- [2026-09-25] Ingest **decision models / « System One »** (Jev/TypeSafe, AnyJev/Nokia, GLiNER2.5-Decide/Fastino) : nouvel axe d'efficacité — **supprimer la génération autorégressive**. Là où Needle 2 *contraint* la génération, ces modèles *lisent* une décision typée calibrée en un passage (AnyJev : tête early-exit à 0,68× d'un forward, CPU, sans entraînement ; GLiNER-Decide : encodeur 340M CPU). Le vrai apport n'est pas l'architecture (GLiNER = classifieur encodeur classique) mais **calibration + contrat de type + seuil d'auto-décision** (part de trafic auto-décidable 7,7 %→52 %). Prolonge la confidence-gate de Needle côté *classification/routing*. Candidat → `../Experiments/exp_decision_model_routing_biblio.md`.
- [2026-09-13] Passe **lab** : synthèse du cluster récent (Needle 2, SwarmLLM, TurboQuant/turbovec). Deux motifs transverses se dégagent et méritent d'être suivis comme *lois de conception* : (1) **data-oblivious / sans calibration** — TurboQuant compresse KV-cache et vecteurs par les maths, rejoint MXFP4 « ne jamais déquantizer » de Kimi K3 ; (2) **frugal localement, escalader/pooler au besoin** — Needle traite tout et escalade sous seuil de confiance, SwarmLLM mutualise le parc. Retombées appliquées → `../Ideas/applied_ideas_2026-09-13.md`.
- [2026-09-13] Ingest **TurboQuant / turbovec** : la compression rejoint la fiche par un nouvel angle — **data-oblivious** (calculée par les maths, pas apprise des données), donc *sans train, sans calibration corpus*, reproductible et RGPD-friendly. Une même méthode sert deux fronts : **KV-cache à 3 bits sans perte** (jusqu'à 8× sur les logits d'attention) et **recherche vectorielle frugale** (16×, CPU-SIMD > FAISS). Rejoint la loi « ne jamais déquantizer » (MXFP4 de Kimi K3) et rouvre le débat lexical vs sémantique du côté `kb.md` (le vector DB redevient abordable localement).
- [2026-09-09] Ingest **SwarmLLM** : ouvre un **axe orthogonal** à tout le reste de la fiche. Jusqu'ici « garder le chaud résident, streamer le froid » depuis le *disque d'une machine* (AirLLM/Colibri/kimi-k3-in-c) ; ici on **répartit les couches sur plusieurs machines** via WebRTC — la ressource mutualisée devient le *parc*, pas le disque local. Confirme aussi WebGPU/WGSL maison comme runtime viable (memory-roofline, > llama.cpp natif sur le même GPU) et le speculative decoding **bit-exact** comme acquis. Deux réserves : prefill série (récurrence Gated-DeltaNet) et activations non privées entre pairs.
- [2026-08-11] Ingest **Needle 2** : le curseur de la frugalité descend à l'extrême bas (14 Mo / ~28 Mo RAM) — non plus « faire tenir le géant sur petit matériel » (AirLLM/Colibri/kimi-k3-in-c) mais **un modèle minuscule complet, on-device**. Apporte deux briques neuves : décodage **contraint par grammaire compilée depuis le schéma** (conformité garantie, pas demandée) et **confidence-gated escalation** (petit modèle partout, gros modèle seulement sous seuil) — motif directement applicable à l'extraction/enrichissement de notices en batch nocturne CPU.
- [2026-08-03c] Ingest **Nano-Capsulator → BabelTele** : nouvel axe d'efficacité *à l'entrée* (compresser le contexte), complémentaire du streaming *des poids*. BabelTele découple lisibilité humaine et décodabilité modèle — tension à surveiller pour la traçabilité biblio.
- [2026-08-03b] Ingest **kimi-k3-in-c** : preuve de concept que l'union « AirLLM (streaming disque) + Colibri (experts MoE routés) + MXFP4 sans déquantization » tient en **176 Ko de C99, 8 Go RAM, sans GPU**. Confirme la piste « MoE × AirLLM » ; question ouverte = passage à un MoE mid-size (bien plus rapide).
- [2026-08-03] Ingest du rapport **Kimi K3** : entrée de l'**attention linéaire à état fixe (KDA)** — l'attention pleine n'est plus la seule voie au long contexte (rejoint Memory Caching) — et du **routage en latent compressé (LatentMoE)**. Ouvre l'axe « manipuler/steerer l'état récurrent ou le latent » (cf. passe d'idées 2026-08-03).
- [2026-08-02] Première population depuis `Inspirations/`. Deux briques fortes entrées : **AirLLM** (streaming disque des couches) et **Colibri** (streaming d'experts MoE, CPU-only) — elles réalisent et *mesurent* l'idée « MoE × AirLLM » des passes d'idées.

## Questions ouvertes / à trancher
- Le débit CPU-only de Colibri (~0,05–1,8 tok/s selon RAM) suffit-il pour de l'enrichissement de notices nocturne sur matériel possédé ? (→ `../OpenQuestions/`)
- Le prefetch d'AirLLM masque-t-il la latence réseau si `layer_shards_saving_path` pointe vers hf-mount/buckets ? (séparer stockage illimité/versionné et compute)

## Candidats d'expériences
- `../Experiments/exp_colibri_notices_nocturne.md`
- `../Experiments/exp_airllm_shards_distants.md`
- `../Experiments/exp_steering_etat_recurrent.md`
- `../Experiments/exp_moe_streaming_midsize.md`
- `../Experiments/exp_compression_contexte_notices.md`
- `../Experiments/exp_needle_extraction_notices.md`
- `../Experiments/exp_swarm_notices_p2p.md`
- `../Experiments/exp_turbovec_rag_notices.md`
- `../Experiments/exp_turboquant_kvcache_notices.md`
- `../Experiments/exp_decision_model_routing_biblio.md`

## Sources dans la base
- **AirLLM**, **Colibri**, **VLM sans encodeur**, **World models**, **HuggingFace ecosystem** — `../Inspirations/llm.md`
- **Inkling-Small** (MoE, encoder-free, effort variable) — `../Inspirations/llm-training.md`
- **Fused tiny local LLMs** — `../Papers/medium_fused-tiny-local-llms.md`
- **Memory Caching (RNN)** — `../Papers/medium_llm-rnn.md`
- **Kimi K3 architecture** (LatentMoE, KDA+MLA, NoPE, AttnRes) — `../Papers/kimi3_architecture_efficiency.md`
- **kimi-k3-in-c** (moteur C99 mono-fichier, experts streamés, MXFP4) — `../Inspirations/llm.md`
- **Needle 2** (SLM 14 Mo on-device, grammaire byte-level, confidence-gated, CQ2-bit) — `../Inspirations/llm.md`
- **SwarmLLM** (inférence P2P layer-shardée navigateur/WebRTC, moteur WebGPU/WGSL maison, MTP spéculatif bit-exact) — `../Inspirations/llm.md`
- **TurboQuant / turbovec** (quantization data-oblivious, KV-cache 3-bit + recherche vectorielle frugale CPU) — `../Inspirations/kb.md`
- **Decision models / System One** (Jev, AnyJev, GLiNER2.5-Decide : décision typée calibrée non-générative, single-pass/early-exit, CPU) — `../Inspirations/llm.md`
- **Compression de contexte (Nano-Capsulator, BabelTele)** — `../Inspirations/kb.md` + `../Papers/2402.18700v2.pdf`, `../Papers/2606.19857v1.pdf`
- _À ingérer :_ Liquid LFM2 encoders (causal decoder → bidirectional encoder) — https://www.liquid.ai/blog/lfm2-5-encoders
