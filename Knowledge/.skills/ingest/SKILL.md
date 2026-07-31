---
name: ingest
description: >
  Ingère une source (URL, PDF, dépôt GitHub, capture, ou note brute) dans la base
  Knowledge/ du dépôt git Inventor. Récupère le contenu, décide où il va, remplit le
  template maison, détecte les doublons, met à jour index.md + log.md, propose des
  cross-links et commit atomiquement. À utiliser dès qu'on dit « ingère »,
  « ajoute ça », « range cette URL », « note cette source », ou qu'on colle
  simplement un lien à classer dans la base Inventor.
---

# Skill : ingest

Transforme une source brute en une note propre dans la base `Knowledge/`, avec un
minimum d'effort humain. Objectif : **on donne une URL (ou un PDF, un repo, une note),
l'agent fait tout le reste** — fetch, classement, résumé, liens, index, log, commit.

Ce skill s'applique au dépôt git **Inventor** : le dossier connecté qui contient
`AGENTS.md`, `SOUL.md` et `Knowledge/`. Il suppose un accès lecture/écriture à ce
dépôt, un outil de récupération de contenu (fetch web, lecture PDF, clone git) et
`git` disponible en ligne de commande.

---

## Arborescence cible (ne pas la réinventer)

```
inventor/                  # racine du dépôt git
├── AGENTS.md              # schema : conventions + workflow (ne pas modifier ici)
├── SOUL.md                # identité de l'agent
└── Knowledge/
    ├── index.md           # catalogue : 1 ligne par entrée — à tenir à jour
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
    ├── Resources/         # actifs réutilisables : datasets, jeux de poids, gros dumps
    ├── Syntheses/         # produit par l'agent
    ├── Ideas/             # produit par l'agent
    ├── Experiments/       # produit par l'agent
    └── OpenQuestions/     # produit par l'agent
```

Dossiers **peuplés par l'humain** (via ce skill) : `Inspirations/`, `Papers/`,
`Resources/`. Les autres sont générés par les passes de synthèse de l'agent.

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
| LLM : pré-entraînement, fine-tuning, architecture | `llm.md` |
| génération de données synthétiques | `synthetic_data.md` |
| rien de ce qui précède | `misc.md` |

Préférer un fichier existant. Créer `Inspirations/<theme>.md` (titre `# <Theme>`) seulement
si un vrai nouveau thème émerge — et dans ce cas, ajouter aussi sa rubrique
(`### <theme>.md — <domaine>`) dans `index.md`. Les fichiers thématiques ne sont pas figés :
l'agent peut en créer de nouveaux quand la taxonomie l'exige.

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

### 7. Mettre à jour index.md et log.md

- **index.md** : ajouter une ligne sous la bonne rubrique — `**<Titre>** — <résumé une ligne>`
  (+ `→ fulltext Papers/...` si Paper). Garder l'ordre existant.
- **log.md** : ajouter en fin de fichier une entrée au format grep-able :

  ```markdown
  ## [AAAA-MM-JJ] ingest | <Titre>
  Source : <url>
  <Classée dans Inspirations/<fichier>.md (+ Papers/... si fulltext) — ou « doublon, entrée enrichie »>.
  Cross-links : <1–3>.
  ```

### 8. Commit atomique

Un commit git par source ingérée, pour un historique lisible :

```bash
git add Knowledge/Inspirations/<fichier>.md Knowledge/Papers/<...>.md \
        Knowledge/index.md Knowledge/log.md
git commit -m "ingest: <Titre> -> Inspirations/<fichier>.md"
```

Si un `.git/index.lock` traîne et bloque (« Operation not permitted » / « index.lock
exists ») : vérifier qu'aucun git ne tourne, puis le supprimer avant de recommencer.
Ne jamais `git add -A` aveuglément : n'ajouter que les fichiers touchés par cet ingest.

---

## Compte-rendu à l'humain (court)

Terminer par 3–5 lignes max :

- **Source** : titre + URL
- **Classée dans** : `Inspirations/<fichier>.md` (+ `Papers/...`) — ou « doublon, entrée enrichie »
- **Cross-links proposés** : 1–3
- **Commit** : le message utilisé
- **Question ouverte retenue** : la plus prometteuse

Ne pas re-narrer chaque étape. L'humain veut le résultat.

---

## Principes (SOUL.md / AGENTS.md)

- Effort humain minimal : une URL doit suffire.
- Compresser, connecter, réutiliser — base plus dense, pas plus grosse.
- Ne jamais dupliquer : enrichir l'existant.
- Un ingest = un commit.
- Concision, pas de buzzwords, distinguer observation / hypothèse / question.
