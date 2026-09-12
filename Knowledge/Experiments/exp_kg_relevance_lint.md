# Expérience : graphe de pertinence 4-signaux + Louvain pour le health-check d'inventor-lint

**Hypothèse** : construire un graphe sur `Knowledge/` avec un **modèle de pertinence 4-signaux**
(inspiré de LLM Wiki / nashsu) — lien direct `[[...]]` ×3, recouvrement de `sources:`/frontmatter ×4,
Adamic-Adar (voisins communs) ×1,5, affinité de type ×1 — puis **détection de communautés Louvain**
donne un health-check *déterministe* bien meilleur que l'heuristique actuelle « titre complet absent
ailleurs » (trop bruyante, cf. `../Syntheses/lint_2026-08-11.md`) : orphelins réels, bridge nodes,
communautés peu cohésives, lacunes — le tout **sans vector DB ni LLM**, sur CPU.

**Protocole minimal** : (1) parser toutes les entrées `Inspirations/*.md` + fichiers `Papers/`,
`Experiments/`, etc. en nœuds ; extraire les arêtes depuis (a) les `[[wikilink]]`/renvois « cf. X
dans Y.md » des *Random Connections*, (b) le partage de source/URL, (c) les voisins communs
(Adamic-Adar), (d) le type (Inspiration/Paper/Experiment…). (2) Calculer le score de pertinence
pondéré, faire tourner Louvain (ex. `graphology-communities-louvain` ou `python-louvain`).
(3) Sortir : pages isolées (deg≤1), communautés à cohésion <0,15, bridge nodes (≥3 clusters),
connexions cross-communauté surprenantes. (4) Comparer les orphelins/liens-manquants trouvés à ceux
listés à la main dans les rapports `Syntheses/lint_*.md`.

**Métrique visée** : précision/rappel de la détection d'orphelins et de cross-links manquants vs les
rapports de lint manuels ; le graphe doit retrouver ≥ ce que le lint humain a noté, avec moins de
faux positifs que l'heuristique par titre. Bonus : les *bridge nodes* pointent-ils les vraies notes
transversales (ex. `synthese_spectre_lisible_modelnative.md`) ?

**Faiblesses / risques** : nos cross-links sont en prose (« cf. X dans Y.md »), pas des `[[wikilinks]]`
stricts → parser à écrire (ou migrer vers des liens typés, cf. OKF v0.2) ; petite base → Louvain peu
stable sous ~quelques centaines de nœuds ; pondérations à recalibrer pour notre topologie.

**Contraintes** : CPU only, zéro vector DB (4 signaux lexicaux/topologiques) ; rester Markdown-canonique
et jetable (le graphe est une vue dérivée, reconstruite à chaque lint — cf. OKF v0.2, exp_fts5).

**Source** : `../Inspirations/kb.md` (LLM Wiki nashsu : modèle 4-signaux, Louvain, graph insights) ;
https://github.com/nashsu/llm_wiki ; à croiser avec `exp_fts5_index_knowledge.md` (index lexical) et
le patron Karpathy (`../Papers/karpathy_llm-wiki.md`).
