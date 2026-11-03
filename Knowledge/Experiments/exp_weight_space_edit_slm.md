# Expérience : réparer un SLM qui boucle par édition de poids guidée par un LLM

**Hypothèse** : sur un petit modèle qui boucle ou dépasse sa limite de tokens, un LLM disponible (Claude ou un open-weights) peut, comme dans Weight-Space Programming, localiser la direction responsable dans quelques couches et l'éditer, avec un gain mesurable sans aucun fine-tuning.

**Protocole minimal** : (1) repro passive : récupérer dans `RikiyaT/lm_understand-logs` l'édition publiée sur Qwen3.5-0.8B, l'appliquer, mesurer sur un sous-ensemble GSM8K (100 items) en CPU — vérifie que l'effet tient hors du harness des auteurs. (2) repro active : donner à un LLM disponible le même accès (poids des MLP + budget de ≤ 50 forward passes sur un sandbox CPU) et lui demander de diagnostiquer les boucles ; comparer son édition à celle publiée. (3) transfert : même démarche sur un SLM biblio qui dérive (ex. génération de vedettes).

**Métrique visée** : taux de réponses tronquées à la limite de tokens et accuracy GSM8K (sous-ensemble), avant/après ; pour (2), recouvrement de la direction trouvée avec celle des auteurs (cosinus).

**Faiblesses / risques** : le LLM « Astra » n'est pas public — l'étape (2) peut échouer pour raison de capacité, pas de méthode ; les forwards d'un 0,8B en CPU sont lents sur de longues générations (plafonner la longueur) ; risque de régression silencieuse sur d'autres tâches (mesurer IFEval en contrôle).

**Contraintes** : CPU only — modèle < 1B, batch=1, sous-ensembles d'éval de 100 items.

**Source** : https://rikiyat.github.io/blog/weight-space-programming/ — `../Inspirations/llm-training.md` ; voisin : `exp_steering_etat_recurrent.md` (steering vs édition gravée dans les poids).
