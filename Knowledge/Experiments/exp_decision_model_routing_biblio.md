# Expérience : routing/classification de demandes en bibliothèque par « decision model » calibré (CPU)

**Hypothèse** : un decision model non-génératif — **AnyJev** (L2 : tête closed-form sur l'état caché d'un
petit Qwen3, ~100–300 labels, CPU, sans entraînement) ou **GLiNER2.5-Decide** (encodeur 340M, zéro
entraînement, labels passés à l'appel) — classe/route des demandes de bibliothèque avec une
**probabilité calibrée seuillable**, permettant d'**automatiser le sûr et d'escalader le douteux**
mieux qu'un prompt LLM : à risque fixé (≤5 % d'erreur), la part de trafic auto-décidable est plus
grande, à coût et latence bien moindres (CPU, un seul passage).

**Protocole minimal** : (1) choisir 2–3 tâches biblio réelles à sortie fermée — tri d'emails d'usagers
(intention + urgence + service), type de document (facture/contrat/notice/thèse…), genre d'un livre à
partir d'un extrait, « faut-il passer la main à un humain ? » ; (2) constituer un petit jeu labellisé
(quelques centaines d'items) ; (3) comparer trois voies sur le *même* jeu : prompt LLM structuré,
GLiNER2.5-Decide (zéro entraînement), AnyJev L0→L2 ; (4) mesurer **exactitude, ECE (calibration),
et surtout la couverture auto-décidable à ≤5 % d'erreur**, plus latence/RAM CPU.

**Métrique de décision** : à risque ≤5 %, quelle fraction du trafic chaque approche automatise sans
supervision ? Le decision model calibré (AnyJev L2 / GLiNER-Decide) dépasse-t-il le prompt LLM en
couverture *et* en coût ? Combien de labels pour que L2 batte L0 sur nos tâches ?

**Faiblesses / risques** : « exactitude » vs un teacher n'est pas la vérité terrain ; L2 est *par
question et par modèle* (une tête ne transfère pas) ; GLiNER2.5-Decide est anglais (prendre
`GLiNER2.5-multi-Decide` pour du multilingue FR) ; la calibration ne sauve pas un modèle qui ne sait
pas répondre.

**Contraintes** : CPU only, local/souverain (RGPD) ; `pip install anyjev[hf]` ou `gliner2` ; aucun
fine-tuning requis (AnyJev L2 = solve closed-form en secondes ; GLiNER-Decide = zéro entraînement).

**Source** : `../Inspirations/llm.md` (« System One » / decision models) ;
https://github.com/nokia-applied-research/AnyJev ; https://huggingface.co/fastino/GLiNER2.5-Decide ;
https://typesafe.ai/blog/introducing-system-one-models-and-jev. À rapprocher de
`exp_needle_extraction_notices.md` (même motif confidence-gated, côté extraction) et du score fondu de
Deja (`../Inspirations/kb.md`).
