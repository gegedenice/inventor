# Expérience : SLM « bibliothécaire » distillé d'un grand modèle

**Hypothèse** : on peut distiller (on-policy, grand teacher → petit élève) un SLM qui approche
le teacher sur **une** tâche de catalogage étroite, tout en étant servable en CPU.
**Protocole minimal** : choisir une tâche (ex. normalisation de vedettes) ; générer un jeu de
distillation depuis un grand modèle ; fine-tuner un petit modèle (QLoRA via Colab CLI/Tinker) ;
comparer élève vs teacher sur un jeu de test tenu.
**Métrique de décision** : écart de qualité élève/teacher sur la tâche < seuil, avec inférence CPU.
**Statut** : proposé.
**Source / fiche** : `../StateOfTheArt/posttraining.md` · **Inkling-Small** (`../Inspirations/llm-training.md`), **MPropositionneur** (`../Inspirations/llm.md`).
