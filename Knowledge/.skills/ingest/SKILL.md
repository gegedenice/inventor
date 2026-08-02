---
name: inventor-ingest
description: >
  Ingère une source (URL, PDF, dépôt GitHub, capture, ou note brute) dans la base
  Knowledge/ du dépôt git Inventor. Récupère le contenu, décide où il va, remplit le
  template maison, détecte les doublons, met à jour log.md (et, pour les sources
  techniques, les fiches StateOfTheArt/ + un candidat Experiments/), propose des
  cross-links et commit atomiquement. N'écrit PAS index.md (réconcilié par inventor-lint).
---

# Skill : inventor-ingest

Transforme une source brute en une note propre dans la base `Knowledge/`, avec un
minimum d'effort humain. Objectif : **on donne une URL (ou un PDF, un repo, une note),
l'agent fait tout le reste** — fetch, classement, résumé, liens, index, log, commit.

Ce skill s'applique au dépôt git **Inventor** : le dossier connecté qui contient
`AGENTS.md`, `SOUL.md` et `Knowledge/`. Si ce dossier n'est pas connecté à la session,
demander à l'utilisateur de le connecter d'abord. Le skill suppose un accès
lecture/écriture au dépôt, un outil de récupération de contenu (fetch web, lecture PDF,
clone git) et `git` disponible en ligne de commande.

## Les deux lentilles (important)

Une même source peut servir **deux intentions** :
- **Mémoire / inspiration** (toujours) — garder trace que ça existe, pour relier plus tard.
- **Opérationnel** (si la source est technique) — nourrir un **état de l'art vivant** et,
  si la technique est reproductible, un **candidat d'expérience**.

Exemple : le billet Liquid « LFM2 encoders » mérite (1) une entrée dans `Inspirations/llm.md`
(mémoire : ce SLM existe) **et** (2) une mise à jour de `StateOfTheArt/inference_archi.md`
(la conversion causal decoder → bidirectional encoder, où elle se situe) **et** (3) un
candidat dans `Experiments/` (« reproduire la conversion sur un petit modèle, mesurer X »).
Un seul ingest, plusieurs retombées. Ne jamais ranger deux fois : on enrichit.

---

## Arborescence cible (ne pas la réinventer)

```
inventor/                  # racine du dépôt git
├── AGENTS.md              # schema : conventions + workflow (ne pas y ranger de contenu ingéré)
├── SOUL.md                # identité de l'agent
└── Knowledge/
    ├── index.md           # catalogue — réconcilié par inventor-lint (NE PAS l'écrire ici)
    ├── log.md             # journal append-only, préfixes datés grep-ables
    ├── Inspirations/      # 1 fichier thématique par domaine (voir routage)
    │   ├── agentic.md     # agents, frameworks, MCP, harnesses
    │   ├── dataviz.md     # visualisation, dashboards
    │   ├── kb.md          # bases de connaissances, moteurs de recherche, RAG
    │   ├── library.md     # bibliothèques/GLAM, OpenAlex, bibliométrie, patrimoine
    │   ├── llm.md         # pré-entraînement / fine-tuning / architecture LLM
    │   ├── synthetic_data.md
    │   └── misc.md        # tout le reste
    ├── Papers/            # fulltext (article/paper) avec frontmatter YAML
    ├── StateOfTheArt/     # fiches d'état de l'art VIVANTES (maintenues, skill inventor-lab)
    │   ├── pretraining.md
    │   ├── posttraining.md
    │   ├── synthetic_data.md
    │   └── inference_archi.md
    ├── Resources/         # actifs réutilisables : datasets, jeux de poids, gros dumps
    ├── Experiments/       # candidats d'expériences + repro/benchmarks (testables soi-même)
    ├── Syntheses/         # produit par l'agent
    ├── Ideas/             # produit par l'agent
    ├── OpenQuestions/     # produit par l'agent
    └── WeakSignals/       # produit par l'agent (skill inventor-ideas)
```

Dossiers **peuplés par l'humain / ce skill** : `Inspirations/`, `Papers/`, `Resources/`,
et — pour les sources techniques — `StateOfTheArt/` et `Experiments/`. Les autres sont
générés par les passes d'idées de l'agent.

---

## Le workflow, étape par étape

### 1. Récupérer la source

- **URL web** : récupérer le contenu. Si la page est rendue en JavaScript et revient
  vide, utiliser un rendu navigateur avant d'abandonner.
- **PDF** : extraire le texte (OCR si scanné).
- **Dépôt GitHub** : lire le README + la structure ; ne pas cloner tout le code.
- **Capture / image** : décrire ce qu'elle montre, garder le fichier dans
  `Inspirations/img/` et le référencer.
- **Note brute** : la prendre telle quelle.

Toujours capturer : **titre**, **URL canonique**, **auteur/source**, **date**.

### 2. Décider du type (Inspiration vs Paper vs Resource)

- **Inspiration** (défaut) — un signal, un outil, un article court, une idée.
  → une **entrée** ajoutée au bon fichier thématique de `Inspirations/`.
- **Paper / article long** à conserver en entier → extraire le **fulltext** dans
  `Papers/<source>_<slug>.md` **ET** ajouter une entrée-pointeur courte dans
  `Inspirations/` référençant `fulltext in @../Papers/<source>_<slug>.md`.
- **Resource** — actif téléchargeable réutilisable (dataset, modèle, gros dump).
  → une entrée dans `Resources/` (même template), + pointeur Inspirations si utile.

En cas de doute : Inspiration.

### 3. Router vers le bon fichier thématique

| Sujet | Fichier |
|---|---|
| agents, frameworks d'agents, MCP, harnesses | `agentic.md` |
| dataviz, dashboards, réseaux sémantiques | `dataviz.md` |
| base de connaissances, recherche, RAG, indexation | `kb.md` |
| bibliothèques, GLAM, OpenAlex, HAL, bibliométrie, patrimoine, métadonnées | `library.md` |
| LLM : architecture (encoders/decoders, MoE, world models), inférence, runtimes | `llm.md` |
| LLM : pré-training / mid-training / post-training, fine-tuning, distillation, RL | `llm-training.md` |
| génération de données synthétiques | `synthetic_data.md` |
| rien de ce qui précède | `misc.md` |

Préférer un fichier existant. Créer `Inspirations/<theme>.md` (titre `# <Theme>`) seulement
si un vrai nouveau thème émerge. Les fichiers thématiques ne sont pas figés : l'agent peut
en créer de nouveaux quand la taxonomie l'exige (la rubrique correspondante sera ajoutée à
`index.md` par `inventor-lint`, pas ici).

### 4. Vérifier les doublons AVANT d'écrire

- Chercher l'URL et le titre dans tout `Knowledge/` :
  `grep -rn "<url ou titre>" Knowledge/`
- Vérifier l'historique des ingests déjà faits :
  `git log --oneline --grep="ingest:"` et `grep "^## \[" Knowledge/log.md`
- Si la source existe déjà : **enrichir l'entrée existante** (compléter Takeaway,
  Questions, Random Connections) plutôt que dupliquer. Le signaler dans le compte-rendu.

### 5. Remplir le template maison

Chaque entrée d'`Inspirations/` (et de `Resources/`) suit **exactement** ce format,
séparée de la précédente par une ligne `---` :

```markdown
## <Titre court et parlant>

<Une phrase factuelle disant ce que c'est.>

Why is it interesting?
- <2 à 4 puces : ce qui est réutilisable / surprenant / transférable>

### Resources

- <URL canonique>
- <liens secondaires éventuels>
- <fulltext in @../Papers/... si Paper>

### Takeaway

"<Citation courte et fidèle de la source — jamais inventée.>"

### Questions

- <1 à 3 questions ouvertes : transfert vers tâches biblio / Inventor>

### Random Connections

- <liens vers d'autres entrées de Knowledge/ : « cf. Airweave dans kb.md »>
```

Règles (voir `SOUL.md` : concis, sans buzzwords) :

- **Takeaway** = citation *réelle* entre guillemets. Rien à citer → laisser vide, ne pas inventer.
- **Why is it interesting** vise le transfert : que voler à cette idée pour bibliothèques,
  agents, dataviz, données synthétiques ?
- **Questions** dans l'esprit Inventor : renverser une hypothèse, comment est-ce résolu
  ailleurs, duplicable pour des tâches de bibliothécaire ?

Pour un **Paper**, `Papers/<source>_<slug>.md` commence par un frontmatter :

```markdown
---
title: "<titre exact>"
description: "<une phrase>"
url: <url canonique>
---

<texte intégral nettoyé>
```

### 6. Cross-links (le cœur de la valeur)

Parcourir les entrées existantes et remplir **Random Connections** avec des renvois
concrets vers des notes voisines déjà présentes (« relie X dans agentic.md à Y dans
kb.md »). C'est ce qui rend la base *plus connectée* dans le temps (cf. `AGENTS.md`).

### 6bis. Lentille opérationnelle (état de l'art + expériences)

Après avoir filé l'entrée Inspiration, se demander : **la source est-elle technique et
opérationnelle** (pré-training, post-training, données synthétiques, optimisation
d'inférence / architectures) ? Si oui :

- **Mettre à jour la fiche `StateOfTheArt/` concernée** (`pretraining.md`,
  `posttraining.md`, `synthetic_data.md` ou `inference_archi.md`) *en place*, pas en
  empilant : ajouter/actualiser la ligne dans « Techniques / approches clés », ajouter une
  entrée datée en haut de « Ce qui a bougé récemment », et référencer la source sous
  « Sources dans la base ». Bumper la date « Dernière mise à jour ».
- **Si la technique est reproductible/testable**, ajouter un candidat dans
  `Experiments/` : `Experiments/<slug>.md` avec l'hypothèse, le plus petit test qui
  tranche, la métrique visée, et le lien vers la source — sous les contraintes CPU/mémoire.

Ne pas forcer : une source non technique (un billet biblio, un outil de dataviz) reste
une simple Inspiration. Cette étape ne concerne que la lentille opérationnelle.

### 7. Mettre à jour log.md (PAS index.md)

`index.md` n'est plus écrit par ce skill : il est réconcilié par `inventor-lint`. Ici on ne
touche qu'au journal.

- **log.md** : ajouter en fin de fichier une entrée au format grep-able :

  ```markdown
  ## [AAAA-MM-JJ] ingest | <Titre>
  Source : <url>
  <Classée dans Inspirations/<fichier>.md (+ Papers/... si fulltext) — ou « doublon, entrée enrichie »>.
  <Fiche SOTA MàJ / candidat Experiments/ si lentille opérationnelle.>
  Cross-links : <1–3>.
  ```

  Utiliser la date du jour réelle (via l'environnement / `date +%F`).

### 8. Commit atomique

Un commit git par source ingérée, pour un historique lisible :

```bash
git add Knowledge/Inspirations/<fichier>.md Knowledge/Papers/<...>.md \
        Knowledge/StateOfTheArt/<fiche>.md Knowledge/Experiments/<...>.md \
        Knowledge/log.md
git commit -m "ingest: <Titre> -> Inspirations/<fichier>.md"
```

(N'ajouter que les fichiers réellement touchés par cet ingest.) Si un `.git/index.lock`
traîne et bloque (« Operation not permitted » / « index.lock exists ») : vérifier
qu'aucun git ne tourne, puis le supprimer avant de recommencer. Jamais de `git add -A` aveugle.

---

## Compte-rendu à l'humain (court)

Terminer par 3–5 lignes max :

- **Source** : titre + URL
- **Classée dans** : `Inspirations/<fichier>.md` (+ `Papers/...`) — ou « doublon, entrée enrichie »
- **Lentille opérationnelle** : fiche SOTA mise à jour + candidat Experiments/ (le cas échéant)
- **Cross-links proposés** : 1–3
- **Commit** : le message utilisé

Ne pas re-narrer chaque étape. L'humain veut le résultat.

---

## Principes (SOUL.md / AGENTS.md)

- Effort humain minimal : une URL doit suffire.
- Une source, éventuellement deux lentilles (mémoire + opérationnel) — mais un seul rangement.
- Compresser, connecter, réutiliser — base plus dense, pas plus grosse.
- Ne jamais dupliquer : enrichir l'existant.
- Un ingest = un commit.
- Concision, pas de buzzwords, distinguer observation / hypothèse / question.
