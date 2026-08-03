# Expérience : sonde latente — prompt vs prompt-BabelTele convergent-ils ?

**Hypothèse** : un prompt lisible et sa version compressée model-native (BabelTele) atterrissent dans des
**régions proches de l'espace latent** du modèle, couche par couche — ce qui expliquerait la fidélité
sémantique élevée (~99,5 %) malgré la perte de lisibilité. Corollaire : si vrai, le « bon » endroit pour
steerer/loger des vecteurs est le latent (là où lisible et compressé se rejoignent), pas le texte.

**Protocole minimal** :
1. 50 paires (prompt original, version BabelTele) — réutiliser/adapter les exemples des papiers + quelques
   notices/contextes biblio.
2. Petit modèle instruct local (CPU), extraire les états cachés par couche (dernier token, ou moyenne).
3. Mesurer, par couche : **CKA** (Centered Kernel Alignment) et similarité cosinus entre représentations des
   deux versions ; + divergence (KL) de la distribution du token suivant.
4. Contrôles : paires *non* équivalentes (prompt A vs BabelTele de B) pour une baseline de « proximité par hasard ».

**Métrique visée** : CKA(orig, babel) nettement > CKA(orig, babel-d'un-autre) sur les couches intermédiaires/hautes,
et KL faible sur le token suivant. Courbe CKA par couche = *où* la convergence se produit (early vs late).

**Ce que ça tranche** :
- Convergence forte → la fidélité s'explique par re-normalisation latente ; argument pour steerer/compresser au niveau latent.
- Convergence faible → la fidélité n'exige PAS la proximité latente (le modèle « recalcule » le sens autrement) — tout aussi instructif.

**Faiblesses / risques** : pair-dépendance (compresseur↔lecteur) ; CKA sensible au choix couche/agrégation ;
petit modèle peut mal décoder le model-native, brouillant la mesure.

**Contraintes** : CPU only ; petit modèle à états cachés accessibles (transformers) ; 50 paires suffisent.

**Source** : `../Papers/2606.19857v1.pdf` (BabelTele), `../Papers/2402.18700v2.pdf` (Nano-Capsulator),
`../Papers/iaetbibliotheques_steering.md`, `../Syntheses/synthese_spectre_lisible_modelnative.md`,
idées « steering dans le latent » (`../Ideas/ideas_2026-08-03.md`).
