# Questions ouvertes — passe 2026-08-03 (Kimi K3 : attention linéaire, latent, QAT)

- **Accès à l'état / au latent** : les runtimes (llama.cpp, vLLM, RWKV/Mamba) exposent-ils l'état
  récurrent et le latent MLA de façon à pouvoir y injecter/extraire un vecteur ? Sans cet accès, le
  steering d'état reste théorique. → idées « steering de l'état récurrent » et « latent LatentMoE/MLA ».

- **Steering par couche vs par état** : lequel tient le mieux un biais sur un long document, et à quel
  coût ? Y a-t-il un régime (contexte court/long) qui favorise l'un ou l'autre ? → idée steering état.

- **QAT natif à petite échelle** : à partir de quelle taille de modèle / quel budget le QAT (entraîner
  dans la précision de déploiement) bat-il nettement la quantization a posteriori pour une tâche biblio ?
  → `Experiments/exp_qat_fp4_slm_biblio.md`.

- **FP4 réel sur CPU** : le vrai FP4 est peu supporté hors GPU récents ; jusqu'où l'int4/int8 simulé
  reproduit-il le bénéfice du QAT natif pour un déploiement CPU souverain ? → posttraining.md, Colibri.

- **Hybride 3:1 (KDA + MLA) transposable petit ?** Le ratio « 3 couches rapides à état fixe + 1 couche
  de récupération exacte » a-t-il un sens à l'échelle SLM biblio (long contexte de notices/fonds) ? →
  inference_archi.md.

## Addendum passe 2 (kimi-k3-in-c)

- **Seuil de réplicabilité mid-size** : à quelle taille de MoE (params actifs) et quelle RAM le streaming
  d'experts en C passe-t-il sous ~1 s/token sur CPU — donc de « batch nocturne » à « interactif » ? →
  `Experiments/exp_moe_streaming_midsize.md`.
- **Taxonomie unifiée du streaming (lien théorique AirLLM/Colibri demandé)** : AirLLM streame les *couches*,
  Colibri/kimi-k3-in-c les *experts routés*, Memory Caching le *KV*, KDA compresse l'*état* — même principe
  « garder le chaud résident, streamer le froid » à 4 niveaux. Peut-on en faire un *planificateur de budget*
  unique (donne un modèle + un matériel → quoi garder résident vs streamer à chaque niveau) ? → llm.md.
- **Dense vs MoE mid-size** : pour un modèle *dense* mid-size, seul le streaming de couches (AirLLM) aide
  (pas d'experts à exploiter) ; le gain frugal est-il alors suffisant, ou faut-il un MoE pour que ça vaille ?

## Addendum passe 3 (compression de contexte : Nano-Capsulator / BabelTele)

- **Garde-fou de fidélité** : la fidélité BabelTele dépend du couple compresseur/lecteur. Un aller-retour
  compress→décompress→compare peut-il servir de *porte QA* avant de faire confiance à un contexte comprimé
  (le désaccord = signal, cf. idée consensus multi-agent) ? → idées passe 3.
- **Frontière lisible/model-native** : quelles zones d'un workflow biblio tolèrent le model-native (cache,
  mémoire, comms agent↔agent) vs exigent le lisible (réponse usager, notice publiée, autorité) ? → kb.md.
- **Capsule texte vs embedding** : une capsule texte compacte peut-elle remplacer un embedding pour le
  contexte d'agent, avec l'avantage d'être éditable/transférable/versionnable ? → idée « notice à deux faces ».

## Addendum passe 4 (portée large de la compression)

- **Discret vs continu** : sur l'axe NL → capsule NL → model-native → soft-prompt → latent, où est le meilleur
  compromis compacité / transférabilité / inspectabilité pour un usage biblio (souveraineté) ? → Synthèse spectre.
- **Skill = source lisible + capsule dérivée** : la régénération automatique de la capsule tient-elle sans dérive,
  et la capsule doit-elle rester NL (audit) ou peut-elle être model-native (frugalité max) ? → idée compression de skills.
- **Où agir : texte ou latent ?** Si prompt et BabelTele convergent dans le latent, faut-il comprimer/steerer au
  niveau latent plutôt que texte ? → `Experiments/exp_sonde_latente_babeltele.md`.
