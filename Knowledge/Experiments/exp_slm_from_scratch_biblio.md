# Expérience : petit SLM « from scratch » pour démo/POC bibliothèque

**Hypothèse** : un SLM jouet (gpt2-style) entraînable en CPU raisonnable suffit pour une démo
pédagogique de bout en bout (pretrain → SFT → DPO) sur un corpus biblio.
**Protocole minimal** : via LLM Builder / notebook « SLM from scratch », pré-entraîner un petit
modèle sur un corpus jouet (notices, résumés), puis SFT + DPO ; observer la courbe de loss.
**Métrique de décision** : le modèle produit-il des complétions cohérentes sur le domaine + la
démo tient-elle dans un temps CPU acceptable ?
**Statut** : proposé.
**Source / fiche** : `../StateOfTheArt/pretraining.md` · **LLM from scratch** (`../Inspirations/llm-training.md`).
