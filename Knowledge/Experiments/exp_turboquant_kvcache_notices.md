# Expérience : KV-cache 3-bit data-oblivious (TurboQuant) pour allonger le contexte d'enrichissement

**Hypothèse** : quantizer le **KV-cache** en 3 bits avec TurboQuant (rotation aléatoire + QJL 1-bit),
**sans entraînement ni calibration corpus** (data-oblivious), réduit l'empreinte KV d'au moins ~6×
sans perte mesurable de qualité sur une tâche de notice — donc un runtime CPU frugal (Colibri,
kimi-k3-in-c) peut traiter un **contexte plus long à RAM égale**, sans ré-entraîner le modèle.
C'est le pendant *inférence-mémoire* de la loi « ne jamais déquantizer » (MXFP4/Kimi K3).

**Protocole minimal** : (1) prendre un petit modèle local servable CPU ; (2) implémenter/le brancher
sur une quantization KV 3-bit type TurboQuant (ou réutiliser une implémentation de référence) ;
(3) mesurer, à contexte croissant (2k → 32k tokens), l'empreinte RAM du KV-cache et la qualité
(perplexité + une tâche concrète : résumé/enrichissement d'une notice à partir d'un contexte long) en
**fp16 vs KV 3-bit** ; (4) mesurer le surcoût runtime (les papiers annoncent un runtime *plus rapide*
que le cache non quantifié sur GPU — à vérifier côté CPU).

**Métrique de décision** : le KV 3-bit tient-il la qualité (écart de perplexité négligeable, tâche de
notice inchangée) tout en divisant l'empreinte KV par ≥ 6× ? À quel contexte max cela repousse-t-il la
limite sur une machine donnée ?

**Faiblesses / risques** : peu de runtimes CPU frugaux exposent un hook de KV-quant → portage non
trivial ; le gain global dépend de la part du KV dans l'empreinte totale (faible sur contexte court) ;
data-oblivious = robuste mais pas forcément optimal par rapport à une quant calibrée.

**Contraintes** : CPU only, mémoire réduite ; rester sans phase d'entraînement (l'atout data-oblivious).

**Source** : `../Inspirations/kb.md` (Turbovec + TurboQuant) ; https://research.google/blog/turboquant-redefining-ai-efficiency-with-extreme-compression/ ;
arXiv:2504.19874 (TurboQuant) + arXiv:2406.03482 (QJL) ; à rapprocher de `exp_qat_fp4_slm_biblio.md`
(quantization *des poids* native) — ici c'est le *KV-cache* qu'on compresse.
