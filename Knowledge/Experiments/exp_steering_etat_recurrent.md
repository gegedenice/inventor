# Expérience : steering de l'état récurrent d'une attention linéaire

**Hypothèse** : dans une attention linéaire à état de taille fixe mis à jour par token (type
Kimi Delta Attention / Mamba / RWKV), injecter un vecteur de steering *dans cet état récurrent*
biaise le comportement de façon **persistante et à coût constant** sur tout un long document —
là où le steering classique agit sur des activations éphémères, couche par couche.

**Protocole minimal** : prendre un petit modèle à attention linéaire (RWKV ou Mamba jouet, CPU) ;
(1) extraire un vecteur « concept » (ex. « répondre en style notice / vocabulaire contrôlé ») par
contraste d'états ; (2) l'ajouter à l'état récurrent à t=0 ; (3) mesurer la persistance du biais
sur 2k–10k tokens vs steering d'activation par couche (baseline). Métrique : dérive du biais (proxy
lexical) au fil du contexte + qualité.

**Métrique visée** : biais maintenu sur ≥ 5k tokens sans réinjection, à coût mémoire constant.

**Faiblesses / risques** : un état trop compressé peut « oublier » le vecteur ; risque de dégrader
la tâche principale ; dépend de l'accès à l'état interne du runtime.

**Contraintes** : CPU only — petit modèle (<1B), séquences longues mais batch=1.

**Source** : `../Papers/kimi3_architecture_efficiency.md` (KDA) ; `../Papers/iaetbibliotheques_steering.md` ;
`../Papers/medium_llm-rnn.md` (Memory Caching) ; idée « vecteurs de steering comme compétences » (`../Ideas/ideas_2026-07-31.md`).
