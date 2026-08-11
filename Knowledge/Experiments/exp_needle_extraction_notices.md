# Expérience : extraction de champs de notice avec Needle 2 (grammaire + confidence, CPU)

**Hypothèse** : un SLM contraint par grammaire byte-level compilée depuis un schéma (Needle 2,
14 Mo / ~28 Mo RAM, éventuellement fine-tuné LoRA sur nos schémas) extrait des **champs de notice
conformes au format** (jamais de sortie hors-schéma) à partir de texte océrisé, sur CPU, avec un
score de confiance calibré permettant d'**escalader au gros modèle seulement le douteux** — au
point de rendre un enrichissement nocturne viable sur matériel possédé.

**Protocole minimal** : (1) décrire un sous-ensemble de champs de notice (ex. auteur, titre, date,
éditeur) comme un schéma d'outil unique ; (2) faire tourner `needle.extract()` sur un lot de textes
océrisés (cf. OCR de 30 000 papiers, `../Inspirations/library.md`) ; (3) mesurer conformité au schéma,
exactitude champ-à-champ, tok/s CPU et RAM ; (4) balayer le seuil de `confidence` et mesurer le
**taux d'escalade** vers un gros modèle ainsi que l'exactitude finale du pipeline à deux étages.
Optionnel : LoRA (`needle finetune`) sur quelques centaines d'exemples synthétisés depuis nos schémas.

**Métrique visée** : trouver le seuil de confiance où l'exactitude finale (petit + escalade) égale
le gros modèle seul, avec un taux d'escalade assez bas pour que le coût/latence global tienne le
batch nocturne CPU. Vérifier que la conformité au schéma reste à 100 % (garantie par la grammaire).

**Faiblesses / risques** : 45 M params — capacité limitée sur champs longs/ambigus (d'où l'escalade) ;
qualité de l'OCR en entrée ; portée de la grammaire (schémas MARC/Unimarc/EAD complets vs sous-ensemble) ;
calibration du score de confiance à re-vérifier sur domaine biblio.

**Contraintes** : CPU only, mémoire réduite ; `pip install cactus-needle`, moteur `.cact` local, hors-ligne.

**Source** : `../Inspirations/llm.md` (Needle 2, Simple Attention Network) ; https://github.com/cactus-compute/needle ;
extraction à deux étages (confidence-gated) — motif « act above threshold, escalate below ».
