# Dataviz

## Dataviz with DeckGL

Why is it interesting?
- Pretty and innovative dataviz
- Run in WebGPU -> many data

### Resources

- https://deck.gl/
- https://medium.com/vis-gl
- Applied to arxiv as semantic network: https://arxiv.vlm.vision/viewer/?init=1&cat=cs

### Takeaway

### Questions

- Could we apply this to all research metadata of an institution (openalex + HAL + recherche.data.gouv + zenodo...) for a very global view?
- Could we aggregate this with Keshif plus dataviz app (based on D3.js): https://github.com/keshifme/keshif-plus. (I use it a lot, example http://explore1.portic.fr/g5data)?

### Random Connections

---

## Prompt Cartography — Design Schemas (chartes de style réutilisables pour agents cartographes)

Bibliothèque de « design schemas » (Ian Muehlenhaus, 2026) : des systèmes de style de carte réutilisables, éditables et prêts pour agents IA. Un schéma est un *style brief* (Markdown/JSON) qui dit à un agent cartographe la palette, la typo, la logique de mise en page, le comportement des symboles, le mobilier de carte et les patterns d'interaction — avant qu'il ne génère. ~25 schémas (objectif ~100), éditables dans le navigateur, licence CC-BY.

Why is it interesting?
- Un schéma = une *charte de style inspectable / éditable / versionnable* (Markdown ou JSON), pas une image. Transférable à toute production dataviz de bibliothèque (une charte maison).
- Empêche la dérive vers le défaut (« same default gray basemap, generic sans-serif labels ») : un brief partagé aligne *une équipe d'agents* sur une intention visuelle — l'idée « Prompt Director ».
- Édition locale Markdown↔JSON, sans compte, puis injection dans l'assistant de son choix — même philosophie « artefact portable » qu'OKF / best_skill.md.
- « Interpretive design systems », pas des copies de cartes existantes (souci de non-contrefaçon) : un brief de style, pas un costume.

### Resources

- https://promptcartography.com/blog/design-schemas-have-arrived.html
- Bibliothèque de schémas : https://promptcartography.com/design-schemas/
- Repo : https://github.com/iMule/promptcartography

### Takeaway

"They turn map-design knowledge into something inspectable, editable, teachable, remixable, and reusable. (…) A schema is not a counterfeit map costume. It is a style brief."

### Questions

- Une charte de style Markdown/JSON pour piloter la production de dataviz patrimoniales (cartes de fonds, frises, réseaux) : mutualisable entre établissements, comme ces schémas ?
- Le schéma comme brief partagé d'une équipe d'agents (Prompt Director) : transposable au-delà des cartes — un brief maison pour rapports / notices / expos, pour éviter la dérive vers le défaut ?
- Design schema (JSON/MD) ↔ OKF / best_skill.md : même famille « artefact portable qui *steer* un agent » — faut-il un format commun ?

### Random Connections

- Dataviz with DeckGL (ce fichier) : le schéma décrit le *style*, DeckGL/Keshif fournit le *moteur* de rendu — combinables.
- OKF (agentic.md) : un design schema est un bundle markdown/JSON portable et éditable, gouvernable comme OKF.
- SkillOpt / OpenSpace (agentic.md) : « le savoir-faire comme artefact texte réutilisable » — le design schema en est la version *design*.
- Steering (llm.md → Papers/iaetbibliotheques_steering.md) : donner une direction stylistique sans fine-tuner — un schéma est un steering en langage naturel côté design.

