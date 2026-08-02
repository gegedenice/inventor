# Idées d'optimisation appliquées — 2026-08-02

Lentille opérationnelle (`inventor-lab`), périmètre : toute la base. Idées **applicables**
sous contrainte CPU/mémoire, ancrées dans l'état de l'art rafraîchi ce jour.

## Enrichissement de notices en batch nocturne, modèle frontière sur matériel possédé

**Où ça s'applique** : enrichissement/complétion de notices (résumés, mots-clés, liens) à volume, en tâche de nuit.
**Technique** : servir un gros MoE via Colibri (streaming d'experts depuis le disque), CPU-only, sans API ni GPU.
**Plus petit test qui tranche** : mesurer le débit (tok/s) de Colibri sur une machine 25–128 Go et estimer le nombre de notices traitables par nuit.
**Gain attendu** : coût d'API = 0, souveraineté des données ; hypothèse : débit suffisant pour du batch (pas de l'interactif).
**Risques / angles morts** : débit à froid très bas (~0,05–0,1 tok/s @25 Go) ; qualité du modèle int4 ; E/S disque.
**Statut** : à tester.
**Source(s)** : `../StateOfTheArt/inference_archi.md`, **Colibri** (`../Inspirations/llm.md`).

## SLM « bibliothécaire » distillé d'un grand modèle, servable CPU

**Où ça s'applique** : une tâche étroite et répétitive (ex. normalisation de vedettes, extraction de champs).
**Technique** : on-policy distillation grand→petit (patron Inkling-Small) façon MPropositionneur (72B→0.6B), puis inférence frugale.
**Plus petit test qui tranche** : distiller sur une seule tâche de catalogage, comparer l'élève au teacher sur un jeu de test tenu.
**Gain attendu** : un SLM spécialisé qui approche le teacher sur la tâche, servable localement.
**Risques / angles morts** : coût de génération des données de distillation ; l'élève perd en factualité/généralité.
**Statut** : hypothèse.
**Source(s)** : `../StateOfTheArt/posttraining.md`, **Inkling-Small** (`../Inspirations/llm-training.md`), **MPropositionneur** (`../Inspirations/llm.md`).

## Séparer stockage et compute : shards de couches sur stockage objet distant

**Où ça s'applique** : servir un modèle plus gros que le disque local, ou partager un même modèle versionné entre nœuds.
**Technique** : pointer le `layer_shards_saving_path` d'AirLLM vers hf-mount / HF buckets ; le prefetch recouvre chargement et calcul.
**Plus petit test qui tranche** : comparer la latence par token en shards locaux vs distants — le prefetch masque-t-il la latence réseau ?
**Gain attendu** : stockage illimité/partagé/versionné, compute sur n'importe quel petit nœud.
**Risques / angles morts** : latence réseau non masquée → effondrement du débit ; coût egress.
**Statut** : hypothèse.
**Source(s)** : `../StateOfTheArt/inference_archi.md`, **AirLLM** + **HuggingFace ecosystem** (`../Inspirations/llm.md`).

## Jeu de données biblio synthétique via harnais type SynthTraces

**Où ça s'applique** : constituer un jeu Q/R de catalogage (UNIMARC/EAD) pour fine-tuner/évaluer un SLM.
**Technique** : harnais à deux modèles — un modèle ouvert distant joue l'expert, un petit modèle local (llama.cpp) joue l'utilisateur qui pose des questions de catalogage.
**Plus petit test qui tranche** : générer 500 paires Q/R, faire évaluer un échantillon par un humain (taux d'exploitables).
**Gain attendu** : données domaine à bas coût côté modèle local.
**Risques / angles morts** : biais/hallucinations du générateur ; besoin d'un filtrage qualité.
**Statut** : à tester.
**Source(s)** : `../StateOfTheArt/synthetic_data.md`, **SynthTraces** (`../Inspirations/synthetic_data.md`).
