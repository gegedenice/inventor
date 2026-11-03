# Signaux faibles — 2026-10-02

Repérés pendant la passe d'idées du 2026-10-02. Pas encore mûrs en idées ; à surveiller.
Format : signal — d'où il vient — pourquoi surveiller.

- **« Calibration + seuil » devient une brique transverse, détachable du modèle** — decision models, Needle 2, Deja, synthèse « décider, pas bavarder » — pourrait devenir une petite bibliothèque réutilisable qui s'applique à n'importe quel classifieur de la base, plutôt qu'une propriété d'un modèle donné.

- **« Le dépôt / le pack est l'unité »** — Agent Plugins, LLM Wiki (distribué comme skill), OKF, patron Karpathy — la distribution (installer, versionner, partager une capacité) devient une préoccupation de premier plan, à côté de la performance. Tendance à suivre pour la mutualisation inter-établissements.

- **« Transformer un ensemble plat en graphe navigable » revient partout** — LLM Wiki (graphe 4 signaux), Graphify, OKF v0.2 (barreau property-graph), idée de graphe de collection — le même besoin se présente sur le code, sur `Knowledge/`, et maintenant sur un catalogue. Candidat à une brique commune « graphe de pertinence sans base vectorielle ».

- **« Data-oblivious / sans calibration sur les données » comme argument de fiabilité** — TurboQuant (compression sans apprentissage), MXFP4 « ne jamais déquantizer » — une méthode qui n'apprend rien des données est reproductible et plus simple à justifier côté RGPD/audit. Pourrait devenir un critère de sélection explicite pour les briques de la base.

- **Le non-génératif gagne du terrain pour les tâches internes** — decision models, extraction par propositions atomiques, GLiNER — pour tout ce qui est décision/classification dans un pipeline, « ne pas générer de texte » devient le choix par défaut, la génération étant réservée à ce qu'un humain lira.
