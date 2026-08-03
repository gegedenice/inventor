# Expérience : QAT natif basse précision pour un SLM bibliothécaire

**Hypothèse** : fine-tuner un petit modèle *nativement* en basse précision (QAT, 4-bit poids)
sur une tâche biblio (ex. normalisation de vedettes, génération de mots-clés) donne un modèle
déployable CPU **sans la perte d'accuracy** d'une quantization a posteriori — le patron « post-training
natif FP4 » de Kimi K3, transposé à petite échelle.

**Protocole minimal** : une tâche + un petit modèle (0.5–1B) ; comparer trois voies sur un jeu test —
(A) fine-tune BF16 puis quantize 4-bit a posteriori ; (B) QAT (fine-tune avec quantization simulée) ;
(C) baseline non quantized. Mesurer accuracy tâche + empreinte mémoire + tok/s CPU.

**Métrique visée** : (B) conserve ≥ 95 % de l'accuracy de (C) à ~¼ de l'empreinte, et bat nettement (A).

**Faiblesses / risques** : outillage QAT plus lourd que la quantization post-hoc ; gains dépendants
de la tâche ; FP4 « vrai » peu supporté sur CPU (se rabattre sur int4/int8 simulé).

**Contraintes** : CPU only ; s'appuie sur les briques déjà notées (Colibri int4, HF jobs pour l'entraînement).

**Source** : `../Papers/kimi3_architecture_efficiency.md` (QAT natif FP4) ; `../Inspirations/llm.md` (Colibri int4) ;
`../Inspirations/llm-training.md` (distillation, LLM from scratch).
