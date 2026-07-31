# Questions ouvertes — passe 2026-07-31

Chaque question est reliée à l'entrée ou l'idée qui la fait naître.

- **Composabilité des vecteurs de steering** : peut-on additionner plusieurs vecteurs
  (format + sujet + langue) sans interférence destructive, ou faut-il les appliquer à des
  couches différentes ? → idée « bibliothèque de vecteurs de steering », `Papers/iaetbibliotheques_steering.md`.

- **Steering vs fine-tuning frugal** : sur une tâche très structurée (générer de l'UNIMARC
  valide), jusqu'où le steering rivalise-t-il avec un QLoRA fait via Colab CLI ? Où est le
  point de bascule ? → idées steering + `agentic.md` Colab CLI.

- **Définition opératoire de « contradiction »** entre deux propositions atomiques : la
  similarité + polarité opposée suffit-elle, ou faut-il une vérification LLM ciblée sur les
  seuls candidats ? → idée « Lint frugal », `llm.md` MPropositionneur.

- **Tokenizer partagé pour la fusion de logits** : quelles familles de petits modèles
  partagent un vocabulaire assez proche pour une fusion au sampler ? Peut-on fusionner
  « un modèle + N vecteurs de steering » au lieu de N modèles (pour tenir la contrainte
  mémoire) ? → idée « conseil de catalogage », `Papers/medium_fused-tiny-local-llms.md`.

- **Fraîcheur et autorité** (question OKF transférée) : qui possède un vecteur de steering,
  un lint_report, un registre de réfutés après une réorg ? Le vrai test reste la gouvernance,
  pas le format. → `agentic.md` OKF.

- **World models pour la mémoire d'agent** : le wiki `Knowledge/` peut-il être traité comme
  un *état* qu'un agent met à jour (prédire l'état plutôt que le token), avec des checkpoints
  compressés à la Memory Caching, permettant un « replay » de la connaissance ? →
  `llm.md` World models, `Papers/medium_llm-rnn.md`, `kb.md` Karpathy LLM-wiki.

- **Génération auto de données contrastives** : un même petit modèle peut-il produire ses
  propres paires contrastives pour extraire son vecteur de steering (auto-supervision du
  contrôle) ? → `synthetic_data.md` SynthTraces + idée steering.

- **Autorité des vedettes générées** : une vedette-matière suggérée par un SLM steeré est-elle
  acceptable pour un catalogueur, et comment tracer/valider ? → idée URL-swap, idée steering.

- **Blockchain / registre distribué** (piste `misc.md`) : un registre horodaté et immuable
  a-t-il un usage réel pour l'autorité/la provenance des notices ou des vecteurs de
  comportement, ou reste-ce une solution en quête de problème ? → `misc.md` Blockchain.
