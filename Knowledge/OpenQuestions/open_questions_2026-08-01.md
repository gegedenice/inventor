# Questions ouvertes — passe 2026-08-01

Ciblée « pertinence sans IA » (déclencheur : Deja). Chaque question reliée à sa source.

- **Markov des zones de catalogage** : la probabilité d'enchaînement de zones/sous-zones
  peut-elle prédire la *prochaine action* du catalogueur (quelle zone remplir ensuite), pas
  seulement la valeur — un accélérateur de workflow ? → `kb.md` Deja, idée autocomplétion.

- **Où est le point de bascule features vs LLM ?** Sur l'autocomplétion de notices, à partir
  de quelle rareté/complexité un score fondu cède-t-il face à un SLM steeré ? Hybride : score
  frugal d'abord, génératif seulement en secours ? → idées autocomplétion + vecteurs de steering.

- **Similarité sans embeddings, jusqu'où ?** Quelles requêtes le RAG « score fondu » rate-t-il
  que les embeddings attrapent (paraphrases, synonymie) ? Un lexique/thesaurus (RAMEAU) comblerait-il
  l'écart à coût CPU ? → idée « RAG sans embeddings », `claim-delta-radar`.

- **Calibrer les crans de flou** : peut-on dériver automatiquement tight/smart/loose depuis la
  distribution des distances observées dans un fichier d'autorités, plutôt que des seuils fixes ?
  → idée « curseur de flou ».

- **Récence en bibliothèque** : la demi-vie d'une semaine de Deja est adaptée à un shell ; quelle
  demi-vie pour un catalogue (saisonnalité des acquisitions, campagnes de rétroconversion) ? →
  `kb.md` Deja.

## Addendum passe 2 (AirLLM / Kimi K3)

- **Prefetch vs latence réseau** : le recouvrement chargement/calcul d'AirLLM suffit-il à rendre
  l'inférence sur shards distants (HF buckets) utilisable, au moins en batch ? → idée AirLLM×HF-buckets.
- **Sparsité de routage MoE** : quel taux d'experts activés par token sur Kimi K3 / Inkling-Small,
  et donc quel gain d'E/S réel pour le streaming sélectif ? → idée MoE×AirLLM.
- **Frugalité assumée en bibliothèque** : servir un modèle frontière sur matériel de récupération
  (hors-ligne, souverain) est-il un argument décisif pour les établissements ? → llm.md AirLLM.
