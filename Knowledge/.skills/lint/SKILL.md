---
name: inventor-lint
description: >
  Réconcilie index.md avec le contenu RÉEL de Knowledge/ (ajouts manuels compris) et
  fait un health-check du wiki : entrées manquantes/orphelines, cross-links absents,
  doublons/contradictions, template incomplet. Puis met à jour log.md et commit.
  inventor-lint est le SEUL propriétaire d'index.md. À utiliser sur « reindexe »,
  « mets à jour l'index », « lint », « vérifie la cohérence de la base », « health-check ».
---

# Skill : inventor-lint

L'opération « Lint » du llm-wiki de Karpathy, appliquée à la base Inventor. Elle rend le
catalogue et la base **cohérents** après des ajouts manuels ou des passes d'autres skills.

**Responsabilité exclusive** : `inventor-lint` est le **seul** skill qui écrit `index.md`.
Les skills `inventor-ingest`, `inventor-ideas` et `inventor-lab` n'écrivent que leur
contenu + `log.md` ; l'index est réconcilié ici, à la demande. `log.md` reste un journal
append-only tenu par chaque skill — lint ne le régénère pas, il y ajoute son entrée.

S'applique au dépôt git **Inventor** (dossier connecté avec `AGENTS.md`, `SOUL.md`,
`Knowledge/`). Si non connecté, demander à l'utilisateur de le connecter. Nécessite `git`.

---

## Étape 1 — Scanner l'arborescence réelle

Établir la liste de ce qui existe vraiment :

- `Inspirations/*.md` → les titres d'entrées : `grep -rn '^## ' Knowledge/Inspirations/`
- `Papers/*.md`, `StateOfTheArt/*.md` → la liste des fichiers.
- `Ideas/*.md`, `OpenQuestions/*.md`, `WeakSignals/*.md`, `Experiments/*.md` → la liste des fichiers.

Détection rapide de dérive (entrées présentes dans les fichiers mais absentes de l'index) :

```bash
cd Knowledge
for f in Inspirations/*.md; do
  grep '^## ' "$f" | sed 's/^## //' | while read -r h; do
    grep -qF "$h" index.md || echo "MANQUE dans index : $h  ($f)"
  done
done
```

---

## Étape 2 — Réconcilier index.md (réconciliation, PAS régénération aveugle)

Objectif : que l'index reflète exactement l'arborescence, **sans perdre** les résumés déjà
rédigés ni l'ordre existant.

- **Entrées manquantes** : pour chaque titre/fichier absent de l'index, écrire une ligne
  `**<Titre>** — <résumé une ligne fidèle>` (+ `→ fulltext Papers/...` si Paper), lue depuis
  l'entrée réelle. La placer sous la bonne rubrique.
- **Entrées orphelines dans l'index** : si une ligne d'index pointe vers un titre/fichier qui
  n'existe plus, la retirer.
- **Rubriques** : si un nouveau fichier `Inspirations/<theme>.md` ou un nouveau dossier existe,
  créer sa rubrique (`### <theme>.md — <domaine>` ou `## <Section>`).
- **Ne pas** réécrire les résumés corrects existants ni réordonner sans raison.

Sections attendues dans `index.md` : Inspirations (par fichier), Papers, Idées, Questions
ouvertes, Signaux faibles, Expériences, État de l'art, Dossiers produits par l'agent.

---

## Étape 3 — Health-check (rapport, correctifs prudents)

Parcourir la base et **signaler** (ne pas casser) :

- **Pages/entrées orphelines** : jamais citées dans une section « Random Connections » ni
  référencées ailleurs.
- **Cross-links manquants** : entrées manifestement liées mais non reliées — les proposer,
  et ajouter celles qui sont évidentes et sûres.
- **Doublons / contradictions** : deux entrées sur la même source, ou deux affirmations qui
  se contredisent entre notes.
- **Template incomplet** : entrée d'`Inspirations/` sans description/`Why is it interesting`
  (un `Takeaway` vide est admis s'il n'y a rien à citer — ne pas le signaler).

Consigner le rapport dans `Syntheses/lint_<date>.md` (constats + suggestions), pour laisser
une trace exploitable et éviter de re-détecter les mêmes points. Corriger d'office
uniquement ce qui est déterministe et sûr (index, cross-links évidents) ; laisser le reste
en suggestions.

---

## Étape 4 — log.md + commit

- **log.md** : ajouter en fin de fichier :

  ```markdown
  ## [AAAA-MM-JJ] lint | reindex + health-check
  <n> entrées ajoutées, <o> orphelines retirées ; <p> constats de santé (voir Syntheses/lint_<date>.md).
  ```

  Date réelle (`date +%F`).
- **Commit atomique** :

  ```bash
  git add Knowledge/index.md Knowledge/log.md Knowledge/Syntheses/lint_<date>.md
  git commit -m "lint: reindex + health-check (<n> ajouts, <o> retraits)"
  ```

  Si `.git/index.lock` bloque : vérifier qu'aucun git ne tourne, le supprimer, recommencer.

---

## Compte-rendu à l'humain (court)

- **Index** : <n> ajouts, <o> retraits
- **Santé** : les 1-3 constats les plus importants (orphelins, contradictions)
- **Rapport complet** : `Syntheses/lint_<date>.md`
- **Commit** : le message utilisé

---

## Principes

- L'index est un artefact *dérivé* : il doit toujours pouvoir être reconstruit du contenu réel.
- Réconcilier, pas écraser : préserver le travail de rédaction déjà fait.
- Signaler avant de corriger ; ne casser aucun contenu de l'utilisateur.
- Une passe = un commit.
