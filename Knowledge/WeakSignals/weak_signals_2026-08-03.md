# Signaux faibles — passe 2026-08-03 (Kimi K3)

Format : `- <signal> — <d'où il vient> — <pourquoi surveiller>`.

- **L'état/latent devient un objet de première classe** — KDA (état fixe par token), MLA (KV compressé
  en un latent), LatentMoE (routage en latent). Surveiller : la valeur se déplace vers des *représentations
  compressées persistantes* — logement naturel du steering, de la signature de document, de la mémoire.

- **Compresser avant de traiter, partout** — LatentMoE compresse avant le routage ; MPropositionneur
  compresse en propositions ; MLA compresse le KV. Surveiller : « down-project puis opère » comme motif
  frugal transversal (moins de bande passante, plus de robustesse).

- **Entraîner dans la précision de déploiement (QAT natif)** — Kimi K3 fait tout le post-training en FP4.
  Surveiller : bascule « quantize après » → « entraîne en basse précision » ; rejoint Colibri int4 et la
  frugalité CPU — un SLM biblio pourrait naître directement 4-bit.

- **L'attention pleine n'est plus la seule voie au long contexte** — KDA/MLA hybride 3:1, Memory Caching,
  world models. Surveiller : consolidation d'une famille « long contexte à mémoire constante » — décisive
  pour lire des fonds/thèses entiers sur CPU.

- **Préserveurs d'accuracy sous contrainte** — AttnRes (résidus par attention apprise), NoPE (position
  implicite). Surveiller : petites astuces qui rendent la sparsité/quantization *sûres* — à connaître avant
  de pousser un SLM biblio en très basse précision.

## Addendum passe 2 (kimi-k3-in-c)

- **La pile d'inférence frontière tient en C99 mono-fichier** — 176 Ko, zéro dépendance, AVX2, déterministe
  cross-OS. Surveiller : un socle d'inférence *souverain, auditable, hors-ligne* devient réaliste pour un
  établissement — argument RGPD/patrimoine fort, à côté de la frugalité.
- **« Garder le chaud résident, streamer le froid » comme loi unique** — couches (AirLLM), experts (Colibri,
  kimi-k3-in-c), KV (Memory Caching), état (KDA). Surveiller : convergence vers une théorie unifiée du
  streaming à niveaux — brique conceptuelle centrale de la fiche inference_archi.
- **Ne jamais déquantizer** — MXFP4 multiplié depuis les nibbles. Surveiller : « opérer directement dans la
  représentation compressée » (poids 4-bit, latent, propositions) comme motif frugal récurrent.
