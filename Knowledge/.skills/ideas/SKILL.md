---
name: inventor-ideas
description: >
  Génère à la demande des idées innovantes à partir de la base Knowledge/ du dépôt
  git Inventor, en suivant la méthode de SOUL.md, puis les archive (Ideas/,
  OpenQuestions/, WeakSignals/) pour que la base s'auto-enrichisse. À utiliser sur
  « génère des idées », « quelles idées à partir de la base », « compile la
  knowledge base », « passe d'idées », « fais tourner Inventor ».
---

# Skill : inventor-ideas

Lit la base de connaissances Inventor et en tire des **idées originales, stress-testées,
puis archivées** dans la base elle-même — pour ne plus avoir à re-prompter à chaque fois,
et pour que chaque passe s'appuie sur les précédentes (les idées se compostent, cf.
llm-wiki : *« good answers can be filed back into the wiki as new pages »*).

S'applique au dépôt git **Inventor** : le dossier connecté contenant `AGENTS.md`,
`SOUL.md`, `Knowledge/`. Si non connecté, demander à l'utilisateur de le connecter.

---

## Contraintes de recherche (voir Knowledge/00_research_notes.md)

- CPU only, mémoire réduite (sauf indication contraire).
- Domaines visés : usages agentiques en bibliothèque, dataviz/dashboarding,
  pré-entraînement & fine-tuning LLM/SLM, génération de données synthétiques.
- Toute idée doit rester réaliste sous ces contraintes.

---

## Périmètre

- **Défaut : toute la base.** Lire `index.md`, puis les fichiers `Inspirations/*.md`
  et survoler `Papers/` selon les pistes.
- Si l'utilisateur nomme un thème (« sur agentic », « sur library »), restreindre à
  ce fichier + ses cross-links.
- S'il dit « depuis la dernière passe » / « en delta », ne raisonner que sur ce qui a
  été ajouté depuis le dernier commit `ideas:` (`git log --oneline --grep="ideas:"`
  puis `git diff <dernier_commit_ideas>..HEAD -- Knowledge/Inspirations Knowledge/Papers`).

Toujours lire d'abord les passes précédentes (`Ideas/`, `OpenQuestions/`,
`WeakSignals/`) pour **ne pas régénérer les mêmes idées** — enrichir ou dépasser.

---

## Méthode (SOUL.md — l'appliquer vraiment, pas la citer)

Pour le matériau lu :

1. Comprendre le problème sous-jacent.
2. Identifier les hypothèses cachées et les contraintes.
3. Chercher des patterns transférables d'autres disciplines (biologie, éco, jeux,
   physique, magie, militaire, design…). Passer les lentilles : *« que ferait un
   mathématicien / un économiste / un game designer / un magicien / un enfant / un
   hacker ? »*, *« et si l'inverse était vrai ? »*, *« quelle hypothèse tout le monde
   accepte sans la questionner ? »*
4. Générer plusieurs approches **radicalement différentes**.
5. Éliminer tout ce qui est inutilement complexe (simple > malin).
6. Ne garder que ce qui pourrait réellement dépasser l'existant.
7. Stress-tester chaque idée, puis l'améliorer une fois de plus.
8. **Habitude secrète** : une fois une bonne idée trouvée, forcer une approche encore
   différente — le vrai déclic vient souvent une itération plus loin.

Distinguer toujours : observation / hypothèse / supposition / preuve.

---

## Artefacts produits (tous datés AAAA-MM-JJ)

### 1. Ideas/ideas_<date>.md — candidats prototypes

Une section `## <Titre de l'idée>` par idée, avec :

```markdown
## <Titre de l'idée>

**Problème / hypothèse renversée** : <ce qu'on questionne ou inverse.>
**Idée** : <la proposition, en 2-3 phrases, sans buzzwords.>
**Plus petit prototype qui la prouve/réfute** : <l'expérience minimale — 1 script,
1 jeu de données, 1 mesure. « Quelle est la plus petite expérience qui tranche ? »>
**Pourquoi ça pourrait dépasser l'existant** : <le levier concret.>
**Faiblesses / risques** : <où ça peut casser.>
**Appuis dans la base** : <cross-links : entrées d'Inspirations/Papers qui la nourrissent.>
**Maturité** : intéressante | plausible | implémentable | à valeur — <laquelle et pourquoi.>
```

### 2. OpenQuestions/open_questions_<date>.md

Liste des questions ouvertes soulevées par la passe, chacune reliée à l'entrée ou
l'idée qui la fait naître.

### 3. WeakSignals/weak_signals_<date>.md — signaux faibles

Connexions inattendues ou tendances naissantes repérées dans la base mais **pas encore
mûres** en idées : deux notes qui se répondent, un pattern qui revient, un domaine qui
émerge. Format court : `- <signal> — <d'où il vient> — <pourquoi surveiller>`. C'est la
matière première du prochain « radar » (cf. Crucix / claim-delta dans la base).

---

## Journal + commit (PAS index.md)

`index.md` est réconcilié par `inventor-lint`, pas ici.

1. **log.md** : ajouter en fin de fichier, format grep-able :

   ```markdown
   ## [AAAA-MM-JJ] ideas | passe <périmètre>
   <n> idées, <m> questions, <k> signaux. Meilleure piste : <titre>.
   ```

   Utiliser la date réelle (`date +%F`).
2. **Commit atomique** — une passe = un commit :

   ```bash
   git add Knowledge/Ideas/ideas_<date>.md \
           Knowledge/OpenQuestions/open_questions_<date>.md \
           Knowledge/WeakSignals/weak_signals_<date>.md \
           Knowledge/log.md
   git commit -m "ideas: passe <date> (<n> idées)"
   ```

   Si un `.git/index.lock` bloque : vérifier qu'aucun git ne tourne, le supprimer, recommencer.

---

## Compte-rendu à l'humain (court)

- **Périmètre** : toute la base / thème X / delta
- **Sorties** : `Ideas/ideas_<date>.md` (<n>), `OpenQuestions/…`, `WeakSignals/…`
- **Top 1–2 idées** : titre + une phrase sur le levier
- **Commit** : le message utilisé

Ne pas recopier toutes les idées dans le chat — elles sont archivées, l'humain les lit
dans les fichiers. Donner juste les meilleures et où trouver le reste.

---

## Principes

- Une idée n'a de valeur qu'une fois testable : toujours proposer le plus petit prototype.
- Ne pas dupliquer les passes précédentes : dépasser, relier, composter.
- Simple > malin, utile > impressionnant (SOUL.md).
- Succès = « je n'y avais pas pensé » de la part d'un expert.
