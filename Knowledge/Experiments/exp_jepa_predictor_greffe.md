# Expérience : greffer un prédicteur JEPA gelé sur un encodeur tiers (scans lacunaires)

**Hypothèse** : un prédicteur I-JEPA gelé, relié à un petit encodeur (DINOv2-S ou CLIP ViT-B/16) par une projection ridge en forme close, améliore la classification sous occlusion *contiguë* forte (≥ 56 %) par rapport à l'encodeur seul masqué — et ce gain se transfère à des numérisations patrimoniales abîmées.

**Protocole minimal** : (1) repro : 1 hôte (DINOv2-S), 1 donneur (prédicteur I-JEPA publié), ImageNet-9 ; pont ridge λ=1e-2 sur ~500 images ; masquage par blocs contigus à K ∈ {0,25 ; 0,56 ; 0,77} ; sonde linéaire par K ; 3 seeds. (2) contrôle : masquage Bernoulli aléatoire (le gain doit disparaître). (3) transfert : quelques centaines de pages numérisées étiquetées (type de document) avec lacunes réelles ou simulées.

**Métrique visée** : Δ accuracy top-1 (hôte+prédicteur vs hôte masqué) par K ; succès = Δ > 0 avec IC hors de zéro à K = 0,77, et Δ ≥ 0 sur le jeu patrimonial.

**Faiblesses / risques** : compatibilité des grilles de patchs hôte/prédicteur (taille de patch, nombre de tokens) ; R² du pont faible (≈0,15–0,29 dans le papier) ; les lacunes réelles ne sont pas des blocs propres ; pas de code publié.

**Contraintes** : CPU only — aucun entraînement de poids ; coût dominé par les forwards ViT sur quelques milliers d'images (batch de nuit acceptable).

**Source** : https://arxiv.org/abs/2607.16274 — `../Inspirations/llm.md` ; terrain : OCR de 30 000 papiers (`../Inspirations/library.md`).
