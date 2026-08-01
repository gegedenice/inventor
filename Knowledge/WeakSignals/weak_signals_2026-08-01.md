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

## Addendum passe 2 (AirLLM / Kimi K3)

- **« Un à la fois » comme motif transversal** — AirLLM (couches), Memory Caching (segments de KV),
  AgentENV (environnements pausés/repris). Surveiller : la même bascule « fit one at a time » se
  répète à toutes les échelles (poids, contexte, environnement) — un principe d'ingénierie frugale.
- **Découplage stockage/compute** — hf-mount + `layer_shards_saving_path` + HF buckets. Surveiller :
  tendance à traiter les poids comme une donnée distante streamée, pas un blob local — rapproche
  l'inférence LLM de l'architecture « données Parquet distantes » d'OpenAlex (library.md).

## Addendum passe 3 (Orca / interface IA)

- **La couche d'orchestration se sépare de l'agent** — Orca (cockpit) vs Claude Code/Codex/Pi (cerveaux).
  Surveiller : la valeur migre vers la « salle de contrôle » (isolation, comparaison, revue, merge) ;
  opportunité pour une salle de contrôle *métier* (bibliothèque) plutôt que *dev*.
- **Mise en concurrence = mécanisme de qualité** — Orca « fan-out puis pick the winner ». Surveiller :
  le pluralisme (plusieurs avis comparés) comme substitut frugal à la confiance dans un seul modèle —
  rejoint la fusion de logits et l'idée de consensus multi-agent.
- **Fin du terminal comme interface d'agents ?** — Orca sort déjà sur mobile/voix. Surveiller : la
  demande d'interfaces non-shell pour piloter des agents ouvre la porte aux publics non-dev (biblio, gouvernance).
