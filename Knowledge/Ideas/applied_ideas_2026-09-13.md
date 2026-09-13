# Idées d'optimisation appliquées — 2026-09-13

Lentille opérationnelle (`inventor-lab`), périmètre : cluster inférence récent (Needle 2,
SwarmLLM, TurboQuant/turbovec, LLM Wiki). Idées **applicables** sous contrainte CPU/mémoire,
ancrées dans `StateOfTheArt/inference_archi.md` rafraîchi ce jour. Complète (ne répète pas) la
passe du 2026-08-02.

## KV-cache data-oblivious 3-bit pour allonger le contexte d'enrichissement, sans train

**Où ça s'applique** : enrichissement/résumé de notices sur un runtime CPU frugal (Colibri,
kimi-k3-in-c) où le KV-cache borne la longueur de contexte utilisable.
**Technique** : quantizer le KV-cache en 3 bits avec TurboQuant (rotation aléatoire + QJL 1-bit),
**data-oblivious donc sans phase d'entraînement ni calibration corpus** — rejoint la loi « ne
jamais déquantizer » (MXFP4/Kimi K3).
**Plus petit test qui tranche** : sur un petit modèle local, mesurer RAM du KV-cache et qualité
(perplexité + une tâche de notice) en fp16 vs KV 3-bit, à contexte croissant.
**Gain attendu** : ≥ 6× sur l'empreinte KV (chiffre annoncé), contexte plus long à RAM égale, sans
ré-entraîner.
**Risques / angles morts** : la plupart des runtimes CPU frugaux n'exposent pas de hook KV-quant ;
portage non trivial ; gain réel dépendant de la part KV dans l'empreinte totale.
**Statut** : à tester → `../Experiments/exp_turboquant_kvcache_notices.md`.
**Source(s)** : `../StateOfTheArt/inference_archi.md`, **Turbovec + TurboQuant** (`../Inspirations/kb.md`),
**Kimi K3 QAT/MXFP4** (`../Papers/kimi3_architecture_efficiency.md`).

## RAG de notices : FTS5 filtre, turbovec rerank dense (hybride frugal, local)

**Où ça s'applique** : recherche/rapprochement sémantique sur un fonds de notices ou de plein-texte
océrisé, sur matériel possédé, air-gapped.
**Technique** : pipeline à deux étages — BM25/FTS5 (déjà candidat `exp_fts5_index_knowledge`) réduit
à une allowlist d'ids, turbovec (TurboQuant 2–4 bit, CPU-SIMD) fait le rerank dense en honorant
l'allowlist *dans* le kernel (pas d'over-fetch). 16× de compression → 10 M vecteurs en ~4 Go.
**Plus petit test qui tranche** : sur un échantillon annoté, comparer rappel@10 de FTS5 seul,
turbovec seul, et l'hybride ; mesurer RAM et latence CPU.
**Gain attendu** : rappel sémantique supérieur au lexical seul, à empreinte tenable sur un poste —
rouvre le « vector DB » que la base avait écarté par coût.
**Risques / angles morts** : qualité de l'embedding local (hors turbovec) ; d faible → TQ+ requis ;
il faut que le gain sémantique *vaille* la brique embedding vs un simple BM25.
**Statut** : à tester → `../Experiments/exp_turbovec_rag_notices.md`.
**Source(s)** : `../StateOfTheArt/inference_archi.md`, **Turbovec + TurboQuant** (`../Inspirations/kb.md`),
weak signal « pertinence sans vector DB » (`../WeakSignals/weak_signals_2026-08-01.md`).

## Extraction de notices « frugal d'abord, escalader le douteux » (Needle confidence-gate)

**Où ça s'applique** : extraction de champs de notice / métadonnées à partir de texte océrisé, en
batch nocturne.
**Technique** : un SLM contraint par grammaire (Needle 2, 14 Mo, sortie conforme au schéma garantie)
traite tout ; le score de confiance calibré route **sous seuil** vers un gros modèle. Deux étages,
coût dominé par le petit.
**Plus petit test qui tranche** : sur un lot océrisé, balayer le seuil de confiance et mesurer le
**taux d'escalade** vs l'exactitude finale du pipeline à deux étages.
**Gain attendu** : exactitude ≈ gros modèle seul, mais fraction seulement du corpus envoyée au gros —
batch nocturne CPU viable, souverain.
**Risques / angles morts** : capacité de 45 M params sur champs longs/ambigus ; calibration du score à
re-vérifier sur domaine biblio ; qualité OCR en entrée.
**Statut** : à tester → `../Experiments/exp_needle_extraction_notices.md`.
**Source(s)** : `../StateOfTheArt/inference_archi.md`, **Needle 2** (`../Inspirations/llm.md`).

## Enrichissement nocturne en pool de postes (SwarmLLM), pas un seul gros nœud

**Où ça s'applique** : faire tourner un modèle mid-size qu'aucun poste ne tient seul, sur le parc
existant d'un service (onglets navigateur, même Wi-Fi).
**Technique** : SwarmLLM répartit les couches sur plusieurs postes (WebRTC, activation 10 Ko sur le
fil) — mutualiser la RAM du parc plutôt que streamer d'un seul disque (axe orthogonal à Colibri/AirLLM).
**Plus petit test qui tranche** : room 2–4 postes, mesurer tok/s agrégé décodage/prefill vs streaming
disque mono-machine sur la même tâche de notice.
**Gain attendu** : débit agrégé exploitable pour du batch, sans GPU serveur ni installation.
**Risques / angles morts** : WebGPU requis (hors contrainte CPU-only stricte, à assumer) ; prefill série
(Gated-DeltaNet) ; **confidentialité** — activations lisibles par un pair, donc intra-service de
confiance seulement.
**Statut** : à tester → `../Experiments/exp_swarm_notices_p2p.md`.
**Source(s)** : `../StateOfTheArt/inference_archi.md`, **SwarmLLM** (`../Inspirations/llm.md`).

## Muscler le health-check d'inventor-lint par un graphe 4-signaux (dogfood)

**Où ça s'applique** : notre propre outillage — le health-check d'`inventor-lint` (détection
d'orphelins/cross-links manquants), aujourd'hui heuristique et bruyant.
**Technique** : graphe sur `Knowledge/` avec le modèle de pertinence 4-signaux de LLM Wiki (lien direct,
recouvrement de `sources:`, Adamic-Adar, affinité de type) + communautés Louvain → bridge nodes, pages
isolées, communautés peu cohésives, **sans vector DB**.
**Plus petit test qui tranche** : comparer les orphelins/liens-manquants trouvés par le graphe à ceux
listés à la main dans les rapports `Syntheses/lint_*.md`.
**Gain attendu** : détection déterministe, moins de faux positifs que l'heuristique par titre.
**Risques / angles morts** : cross-links en prose (« cf. X dans Y.md ») à parser ; petite base → Louvain
peu stable ; pondérations à recalibrer.
**Statut** : à tester → `../Experiments/exp_kg_relevance_lint.md`.
**Source(s)** : **LLM Wiki (nashsu)** (`../Inspirations/kb.md`).
