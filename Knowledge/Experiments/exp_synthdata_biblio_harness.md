# Expérience : jeu Q/R de catalogage synthétique via harnais type SynthTraces

**Hypothèse** : un harnais à deux modèles (grand distant = expert, petit local = utilisateur)
génère un jeu Q/R de catalogage (UNIMARC/EAD) suffisamment exploitable pour fine-tuner/évaluer.
**Protocole minimal** : reprendre le patron SynthTraces ; petit modèle local (llama.cpp) pose des
questions de catalogage, modèle ouvert distant répond ; générer 500 paires ; échantillon évalué
par un humain.
**Métrique de décision** : taux de paires « exploitables » ≥ seuil (à fixer).
**Statut** : proposé.
**Source / fiche** : `../StateOfTheArt/synthetic_data.md` · **SynthTraces** (`../Inspirations/synthetic_data.md`).
