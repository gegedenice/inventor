---
name: inventor-lab
description: >
  Lentille OPÉRATIONNELLE d'Inventor. Maintient les fiches d'état de l'art vivantes
  (StateOfTheArt/) à partir des ingests récents, puis génère des idées d'optimisation
  APPLIQUÉES au contexte (CPU/mémoire) et des candidats d'expériences reproductibles.
  À utiliser sur « mets à jour l'état de l'art », « revue technique », « passe lab »,
  « idées d'optimisation », « qu'est-ce que je peux tester », « veille training/inférence ».
---

# Skill : inventor-lab

La lentille **convergente** d'Inventor, complémentaire de `inventor-ideas` (divergente).
Là où `inventor-ideas` relie des choses éloignées pour faire jaillir des idées originales
pour l'entreprise, `inventor-lab` **approfondit un domaine technique précis** : il tient
à jour un état de l'art opérationnel et en tire des optimisations testables dans *ta* stack.

Domaines couverts (cf. `StateOfTheArt/`) : pré-training, post-training, données
synthétiques, optimisation d'inférence & architectures.

S'applique au dépôt git **Inventor** (dossier connecté avec `AGENTS.md`, `SOUL.md`,
`Knowledge/`). Si non connecté, demander à l'utilisateur de le connecter.

## Mentalité (différente de inventor-ideas)

Ingénieur, pas inventeur tous azimuts. Valorise : actualité, rigueur, reproductibilité,
mesure avant conclusion (cf. principes d'ingénierie d'`AGENTS.md`). Toujours distinguer
**observation / hypothèse / supposition / preuve**. Ne jamais fabriquer un résultat ni une
citation : une fiche ne contient que ce que les sources de la base soutiennent.

Contraintes du contexte : **CPU only, mémoire réduite** (cf. `00_research_notes.md`).
Toute optimisation ou expérience proposée doit être réaliste sous ces contraintes.

---

## Périmètre

- **Défaut : les quatre fiches** de `StateOfTheArt/`.
- Si l'utilisateur nomme un domaine (« l'inférence », « le post-training »), ne traiter
  que la fiche correspondante.
- « Nouveautés » = ce qui a été ingéré depuis la dernière passe lab :
  `git log --oneline --grep="lab:"` pour dater la dernière passe, puis
  `git log --oneline --grep="ingest:" --since=<date>` et
  `git diff <dernier_commit_lab>..HEAD -- Knowledge/Inspirations Knowledge/Papers`.

---

## Étape 1 — Rafraîchir les fiches d'état de l'art (maintenance EN PLACE)

Pour chaque fiche visée :

1. Lire la fiche, puis les entrées `Inspirations/` et `Papers/` qu'elle référence, plus
   les ingests récents pertinents.
2. **Mettre à jour la fiche en place** (on révise, on n'empile pas) :
   - **En bref** : réécrire les 2-4 phrases si l'état du domaine a bougé.
   - **Techniques / approches clés** : `technique — statut (émergent/établi/déclinant) —
     quand l'utiliser — source(s)`. Actualiser les statuts, fusionner les doublons,
     signaler les contradictions entre sources.
   - **Ce qui a bougé récemment** : ajouter une ligne datée EN HAUT (`- [AAAA-MM-JJ] …`).
   - **Sources dans la base** : compléter les cross-links.
   - Bumper « Dernière mise à jour ».
3. Ne rien inventer : si une info manque, la mettre en « Questions ouvertes » plutôt que
   de combler au jugé.

---

## Étape 2 — Idées d'optimisation appliquées

À partir de l'état de l'art rafraîchi, générer des optimisations **applicables à la stack**
(convergent, pas blue-sky). Écrire dans `Ideas/applied_ideas_<date>.md`, une section
`## <Titre>` par idée :

```markdown
## <Titre de l'optimisation>

**Où ça s'applique** : <point précis de ta stack / workflow visé.>
**Technique** : <ce qu'on applique, tiré de l'état de l'art — avec la source.>
**Plus petit test qui tranche** : <expérience minimale, CPU/mémoire OK.>
**Gain attendu** : <métrique + ordre de grandeur, en hypothèse.>
**Risques / angles morts** : <où ça peut ne pas tenir.>
**Statut** : hypothèse | à tester | validé | écarté.
**Source(s)** : <cross-links StateOfTheArt/ + Inspirations/Papers.>
```

Distinguer les idées **appliquées** (ce fichier) des idées **divergentes** produites par
`inventor-ideas` (`Ideas/ideas_<date>.md`).

---

## Étape 3 — Candidats d'expériences

Pour les 1-3 pistes les plus prometteuses, créer/actualiser `Experiments/<slug>.md` :

```markdown
# Expérience : <titre>

**Hypothèse** : <ce qu'on cherche à prouver/réfuter.>
**Protocole minimal** : <étapes, modèle/jeu de données jouet, sous CPU/mémoire.>
**Métrique de décision** : <ce qui tranche.>
**Statut** : proposé | en cours | fait — <résultat si fait.>
**Source / fiche** : <lien StateOfTheArt/ + source.>
```

Quand une expérience est menée, consigner le résultat ici et répercuter l'apprentissage
dans la fiche `StateOfTheArt/` correspondante (implémentation ↔ recherche se renforcent).

---

## Étape 4 — Journal + commit (PAS index.md)

`index.md` est réconcilié par `inventor-lint`, pas ici.

1. **log.md** : entrée grep-able en fin de fichier :

   ```markdown
   ## [AAAA-MM-JJ] lab | revue <périmètre>
   <n> fiches rafraîchies, <m> idées appliquées, <k> expériences. Priorité : <titre>.
   ```

   Date réelle (`date +%F`).
2. **Commit atomique** :

   ```bash
   git add Knowledge/StateOfTheArt/ Knowledge/Ideas/applied_ideas_<date>.md \
           Knowledge/Experiments/ Knowledge/log.md
   git commit -m "lab: revue <périmètre> (<n> fiches, <m> idées appliquées)"
   ```

   Si `.git/index.lock` bloque : vérifier qu'aucun git ne tourne, le supprimer, recommencer.

---

## Compte-rendu à l'humain (court)

- **Périmètre** : quelles fiches rafraîchies
- **Ce qui a bougé** : 1-2 changements notables dans l'état de l'art
- **Top idée appliquée** : titre + gain attendu + le test qui tranche
- **Prochaine expérience à lancer** : laquelle et pourquoi
- **Commit** : le message utilisé

Ne pas recopier tout le contenu : il est archivé, l'humain le lit dans les fichiers.

---

## Principes

- Mesurer avant de conclure ; le plus petit prototype qui tranche d'abord.
- Maintenir, ne pas empiler : une fiche d'état de l'art se révise en place.
- Observation ≠ hypothèse ≠ preuve — toujours le préciser.
- Réaliste sous contrainte CPU/mémoire, sinon le noter comme limite.
- Une passe = un commit.
