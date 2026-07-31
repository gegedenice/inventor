# Signaux faibles — passe 2026-07-31

Connexions inattendues repérées dans la base, pas encore mûres en idées.
Format : `- <signal> — <d'où il vient> — <pourquoi surveiller>`.

- **Convergence « connaissance = artefact versionné »** — OKF + Karpathy LLM-wiki + le dépôt
  Inventor lui-même — trois instances du même pattern (markdown + frontmatter + index + log).
  Surveiller : le pattern se standardise ; positionner Inventor comme cas d'étude / dogfooding.

- **Le steering comme alternative transversale au fine-tuning** — billet steering + Colab CLI
  (fine-tuning) qui se répondent. Surveiller : si le steering tient sur des tâches biblio, une
  partie du besoin de fine-tuning frugal disparaît (moins d'entraînement, plus d'arithmétique
  de vecteurs).

- **Propositions atomiques comme unité de travail universelle** — MPropositionneur revient
  partout (index sur docs, résumés, claim-delta, Lint proposé). Surveiller : c'est peut-être
  la brique CPU centrale de la base (indexation, dédup, contradiction, delta) plutôt qu'un
  outil ponctuel.

- **Le harnais Pi comme couteau suisse** — moonbot, Flue, SynthTraces, PI packages : Pi sert
  d'agent, de générateur de données, de framework. Surveiller : un même harnais minimal
  couvre production ET génération de données d'entraînement — boucle fermée modèle↔données.

- **Fusion runtime > merge statique** — l'article fusion de logits insiste : combiner à
  l'inférence (steering, vote de logits) sans jamais modifier les poids. Surveiller : famille
  cohérente « composition sans entraînement » (vecteurs + votes) opposée au fine-tuning/merge.

- **Le delta plutôt que le temps réel** — OSINT/Crucix (temps réel, dur en biblio) vs
  claim-delta (deux fenêtres, faisable). Surveiller : recadrer tout « dashboard de pilotage »
  demandé comme un radar de delta sur snapshot, pas un flux live.

- **URL comme interface d'agent** — autoarxiv + raccourcis URL de « reading research papers ».
  Surveiller : motif récurrent « détourner un identifiant existant » comme substitut gratuit à
  une UI — applicable au-delà des notices (DOIs, handles, ark).

- **Le négatif est du signal** — claims *disparaissant* (claim-delta) + consigne « ne pas
  régénérer les mêmes idées ». Surveiller : les impasses et réfutations méritent peut-être un
  artefact de première classe (cf. idée « anti-bibliothèque »).
