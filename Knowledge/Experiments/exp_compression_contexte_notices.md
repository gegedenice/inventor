# Expérience : compression de contexte (lisible vs model-native) sur des notices

**Hypothèse** : compresser un lot de notices / un contexte documentaire en forme *compacte*
préserve assez de sémantique pour une tâche aval (Q/R, résumé, sélection) tout en réduisant
fortement les tokens — et la variante **model-native** (BabelTele) gagne encore en compacité
au prix de la lisibilité humaine, sans perte de fidélité notable.

**Protocole minimal** : prendre 50 notices + 10 questions aval. Comparer 3 régimes de contexte
sur un même petit LLM local : (A) texte brut, (B) compression NL lisible (style Nano-Capsulator,
via prompt de compression), (C) compression model-native (style BabelTele : abréviations, symboles,
fragments). Mesurer : tokens de contexte, exactitude aval, et *traçabilité* (peut-on remonter à la
source ?). 

**Métrique visée** : (B) et (C) conservent ≥ 95 % de l'exactitude de (A) à < 40 % des tokens ;
quantifier le surcroît de compacité de (C) et sa perte de lisibilité/traçabilité.

**Faiblesses / risques** : dépend du couple compresseur/lecteur (même famille de modèle ?) ;
(C) casse la traçabilité (rédhibitoire pour une réponse publiée) → à réserver aux caches/mémoire ;
petit LLM local peut mal décoder le model-native.

**Contraintes** : CPU only ; petit modèle instruct local ; jeu de notices réel (export catalogue).

**Source** : `../Inspirations/kb.md` (compression de contexte) ; `../Papers/2402.18700v2.pdf` (Nano-Capsulator) ;
`../Papers/2606.19857v1.pdf` (BabelTele) ; `../Inspirations/llm.md` (MPropositionneur, alternative lisible).
