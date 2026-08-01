# Signaux faibles — passe 2026-08-01

Déclencheur : Deja. Format : `- <signal> — <d'où il vient> — <pourquoi surveiller>`.

- **« Pertinence sans vector DB » devient une famille** — Deja (features fondues), Graphify
  (topologie = similarité), QMD (BM25 + re-rank local). Surveiller : convergence vers un socle
  de récupération CPU-only, explicable, opposé au réflexe embeddings/RAG.

- **Le contexte courant comme feature centrale** — le « répertoire courant » de Deja. Surveiller :
  transposer « l'état courant conditionne la suggestion » à la notice en cours, à la session du
  catalogueur, au corpus interrogé — un principe plus qu'un outil.

- **Le compromis mis dans la main de l'usager** — curseur de flou tight/smart/loose. Surveiller :
  design d'interaction où un réglage remplace un hyperparamètre caché (recall/precision, autorité,
  seuil de dédoublonnage).

- **Modèles de Markov d'actions** — la probabilité d'enchaînement de Deja. Surveiller : chaînes
  d'ordre 1 sur les workflows (catalogage, acquisition) comme prédicteurs ultra-légers, à côté
  des world models « prédire l'état » de llm.md — deux échelles du même geste.

- **Frugalité assumée comme position** — « aucune IA, pure algorithmie » revendiqué. Surveiller :
  contre-tendance à l'IA-partout ; argument fort en bibliothèque (souveraineté, coût, explicabilité,
  RGPD) qui rejoint le steering (contrôle léger) et le pré-entraînement SLM (petits modèles).
