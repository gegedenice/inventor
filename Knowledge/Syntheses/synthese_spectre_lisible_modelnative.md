# Synthèse — Le spectre lisible ↔ model-native : une même question à tous les étages

> 2026-08-03 · Note transversale (déclenchée par la lignée Nano-Capsulator → BabelTele, `../Inspirations/kb.md`).
> Objet : relier ce qui était rangé sous « compression de contexte » à des thèmes plus larges — inférence,
> skills, latent — car il s'agit du **même geste** décliné à plusieurs endroits.

## La thèse

Représenter du sens pour un LLM, ce n'est pas forcément écrire du langage naturel lisible. Il existe un
**spectre continu** de représentations, du plus lisible-humain au plus model-native :

`NL brut  →  capsule NL lisible (Nano-Capsulator)  →  discret model-native (BabelTele)  →  soft-prompt continu  →  latent / poids`

L'axe qui compte pour Inventor n'est pas « compressé ou non » mais **discret (texte, inspectable, transférable,
versionnable) vs continu (vecteur opaque, lié à un modèle)**. Nano-Capsulator et BabelTele restent du *texte* ;
le soft-prompt et l'embedding basculent dans l'opaque. Pour un contexte de bibliothèque (souveraineté, audit,
traçabilité), le discret est presque toujours préférable — même non lisible, il reste un artefact qu'on peut
diff, versionner, transférer.

## Le même geste à quatre étages

1. **Contexte / prompt** — compresser l'entrée : Nano-Capsulator (lisible) → BabelTele (model-native).
   Levier de frugalité *à l'entrée*, complémentaire du streaming *des poids* (AirLLM/Colibri).
2. **Skill** — un skill (`best_skill.md` de SkillOpt, skills OpenSpace/Acontext, bundle OKF) est un prompt
   persistant : donc compressible. Mais tension frontale avec sa raison d'être (inspectable, gouvernable).
3. **Mémoire d'agent** — cache et canal inter-agents peuvent être model-native (Acontext, idée « model-native
   dedans, lisible dehors »).
4. **Poids / latent** — MXFP4 (opérer sans déquantizer), latent MLA/LatentMoE, état récurrent KDA : compresser
   la *représentation interne* — là où vivent aussi les vecteurs de steering.

## La règle biblio (résolution de la tension)

Partout où un humain lit ou vérifie (réponse à l'usager, notice publiée, autorité, skill gouverné) :
**la source lisible reste la vérité versionnée ; le compressé/model-native n'est qu'un dérivé régénérable**
pour l'exécution frugale. Jamais l'un sans pouvoir reconstruire l'autre. C'est le pendant, côté représentation,
de « garder le chaud résident, streamer le froid » côté ressources.

## La question théorique ouverte

Pourquoi la fidélité tient-elle (99,5 % à ~28 % du volume) ? Hypothèse : un prompt et sa version BabelTele
**convergent dans l'espace latent** du modèle (le modèle re-normalise vers une représentation interne proche).
Si vrai, cela unifie tout : compresser le texte et manipuler le latent seraient deux accès au *même* lieu — et
le bon endroit pour steerer/loger des vecteurs serait le latent, pas le texte. Sonde minimale : CKA / similarité
des états cachés par couche entre (prompt, BabelTele-prompt) → `../Experiments/exp_sonde_latente_babeltele.md`.
Résultat négatif tout aussi instructif : la proximité latente ne serait pas *nécessaire* à la fidélité.

## Sources dans la base
- **Compression de contexte** (Nano-Capsulator, BabelTele) — `../Inspirations/kb.md` + `../Papers/2402.18700v2.pdf`, `../Papers/2606.19857v1.pdf`
- **Steering / latent** — `../Papers/iaetbibliotheques_steering.md`, `../Ideas/ideas_2026-08-03.md`
- **Skills comme artefacts** — SkillOpt, OpenSpace, Acontext, OKF (`../Inspirations/agentic.md`)
- **Compression de représentation interne** — MXFP4/KDA/MLA (`../Inspirations/llm.md`, `../Papers/kimi3_architecture_efficiency.md`)
- **Propositions atomiques** (compression *lisible* structurée) — `../Inspirations/llm.md`
