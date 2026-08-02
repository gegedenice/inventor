# Expérience : shards de couches AirLLM sur stockage objet distant

**Hypothèse** : le prefetch d'AirLLM masque la latence réseau quand les shards de couches sont
sur hf-mount / HF buckets plutôt qu'en local — rendant viable la séparation stockage/compute.
**Protocole minimal** : sauver les shards d'un modèle (ex. 8–13B) localement puis sur hf-mount ;
mesurer la latence/token dans les deux cas ; activer/désactiver le prefetch pour isoler son effet.
**Métrique de décision** : surcoût de latence distant vs local < seuil acceptable pour du batch.
**Statut** : proposé.
**Source / fiche** : `../StateOfTheArt/inference_archi.md` · **AirLLM** + **HuggingFace ecosystem** (`../Inspirations/llm.md`).
