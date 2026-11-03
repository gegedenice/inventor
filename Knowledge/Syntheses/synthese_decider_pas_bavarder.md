# Synthèse — Décider, pas bavarder : contrat de type + calibration + seuil d'auto-décision

> 2026-10-02 · Note transversale (déclenchée par l'arrivée des *decision models* dans `../Inspirations/llm.md`,
> signalée comme cluster montant aux lints 2026-09-25 et 2026-10-02).
> Objet : relier des entrées rangées séparément — Needle 2, les decision models « System One », Deja,
> l'extraction de propositions atomiques — car elles partagent **le même geste** : produire une *décision*
> utilisable par du code, pas un texte à relire.

## La thèse

La plupart des usages « IA en bibliothèque » qu'on vise ne demandent pas une *conversation* : ils demandent une
**décision** qu'un programme consomme — router une demande, classer un document, trier un email, noter une
urgence, extraire un champ, dire « faut-il passer la main à un humain ? ». Pour ça, générer du texte est le
mauvais outil : coûteux, lent, parseable de travers, hallucinable. Une famille de briques converge vers un autre
contrat, qu'on peut résumer en trois invariants **indépendants du modèle** :

1. **Contrat de type** — la sortie est une valeur typée conforme à un schéma décidé à l'avance (pas de texte
   libre à parser, pas de type-error).
2. **Calibration** — chaque décision porte une probabilité *honnête* : plus de confiance = plus d'exactitude.
3. **Seuil d'auto-décision** — on agit au-dessus du seuil, on **escalade** (humain ou gros modèle) en dessous.
   La métrique qui compte n'est pas l'exactitude brute mais la **part de trafic auto-décidable à risque fixé**.

Le levier n'est pas l'architecture du modèle — c'est ce triplet, greffable sur à peu près n'importe quel
substrat.

## Le même geste à quatre endroits de la base

1. **Classification / routing non-génératif** — les *decision models* (`llm.md`) : Jev (propriétaire) et le
   roster open-weights (famille GLiNER2.5-Decide, Laya, JevK5… ; AnyJev comme adaptateur sur tout LLM). Décision
   typée + proba calibrée lue en *un passage*. C'est le cas pur.
2. **Génération d'appels d'outils / extraction** — **Needle 2** (`llm.md`) : même contrat côté *génération
   contrainte* — grammaire byte-level (conformité au schéma garantie), confidence-gated, escalade sous seuil.
   Needle contraint la génération ; les decision models la suppriment. Deux faces, même contrat.
3. **Pertinence sans IA** — **Deja** (`kb.md`) : décider par un **score seuillable** fondu de signaux, zéro
   modèle. Montre que le triplet ne *requiert* pas de LLM — ce qui manquait à Deja, c'est la *calibration* de
   son score. Borne basse frugale de la famille.
4. **Structuration de texte en unités discrètes** — **propositions atomiques / MPropositionneur** (`llm.md`),
   **ContextGem** (`kb.md`) : « texte → valeur typée ». ContextGem = le gros LLM long-contexte qui *extrait* ;
   les decision models = le petit modèle calibré qui *décide*. Deux bouts du même axe.

## La règle biblio (quand bavarder, quand décider)

Par défaut, **décider**. Réserver la génération de texte aux seuls points où un humain lira la sortie (réponse
rédigée à l'usager, résumé de notice destiné à publication). Partout ailleurs — dans un pipeline, derrière une
file, en tâche de nuit — préférer une décision typée + calibrée + seuil. Corollaire souveraineté/RGPD : ces
briques tournent **sur CPU, en local, sans API** (GLiNER2.5-Decide 340M, AnyJev sur petit Qwen3), donc la
décision ne quitte pas l'établissement. C'est le pendant « côté sortie » du « garder le chaud résident,
streamer le froid » : *automatiser le sûr, escalader le douteux*.

## La question ouverte / à trancher

Le seuil d'auto-décision est le vrai paramètre produit, pas l'exactitude. **Où le place-t-on pour une tâche de
bibliothèque, et qui en assume le risque résiduel ?** Un routeur de demandes à 95 % d'exactitude n'automatise
rien s'il ne sait pas *dire* quand il est dans les 5 % ; bien calibré, il peut automatiser la majorité et ne
faire remonter que l'ambigu. Sous-questions : la calibration tient-elle sur du **français** (bancs actuels
anglais → GLiNER2.5-multi-Decide, ou AnyJev sur LLM multilingue) ? Un seul seuil global, ou un seuil par type de
décision (router vs « passer à un humain » n'ont pas le même coût d'erreur) ? Le même score calibré peut-il
**gater** aussi bien une classif (decision model) qu'une génération (Needle) dans un pipeline unifié ?

## Expériences qui testent cette synthèse

- `../Experiments/exp_decision_model_routing_biblio.md` — routing/classif biblio calibré, **couverture
  auto-décidable à ≤5 % d'erreur** vs prompt LLM (le cœur de la thèse).
- `../Experiments/exp_needle_extraction_notices.md` — extraction confidence-gated, taux d'escalade vs exactitude.
- Idée « curseur de flou recall/precision » & autocomplétion par score fondu (`../Ideas/ideas_2026-08-01.md`) —
  le seuil comme objet réglable, côté Deja.

## Sources dans la base

- **Decision models / « System One »** (Jev + roster open-weights, AnyJev, GLiNER2.5-Decide) — `../Inspirations/llm.md`
- **Needle 2** (génération contrainte, confidence-gated) — `../Inspirations/llm.md`
- **SLM extraction de propositions atomiques** (texte → unités typées) — `../Inspirations/llm.md`
- **Deja** (décision par score fondu, sans IA) — `../Inspirations/kb.md`
- **ContextGem** (extraction structurée déclarative) — `../Inspirations/kb.md`
- **Questions ouvertes** calibrage/seuils — `../OpenQuestions/open_questions_2026-08-01.md`
