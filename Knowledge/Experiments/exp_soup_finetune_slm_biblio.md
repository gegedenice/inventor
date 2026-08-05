# Expérience : fine-tune one-command d'un SLM bibliothécaire avec Soup

**Hypothèse** : Soup (« un YAML, une commande ») permet de fine-tuner un petit modèle sur une tâche
biblio ciblée (ex. normalisation de vedettes, génération de mots-clés RAMEAU) en QLoRA sur matériel
modeste (8 Go VRAM, ou CPU pour test), avec un effort d'infrastructure quasi nul — plus simple que
le fine-tuning délégué (Colab CLI) pour un établissement qui veut rester local/souverain.

**Protocole minimal** :
1. Jeu SFT (~500–2000 exemples) issu d'un export catalogue (paires instruction→sortie).
2. `soup init --template chat` + un `soup.yaml` (base 1–7B, task sft, quantization 4bit, lora r=32).
3. `soup train` en QLoRA ; `soup eval` sur un jeu tenu à l'écart ; `soup export --format gguf` pour Ollama.
4. Comparer : exactitude tâche, VRAM/temps, et effort humain (nb d'étapes) vs un pipeline TRL/Colab CLI.

**Métrique visée** : atteindre une exactitude tâche « utile » (≥ baseline zero-shot du modèle de base + marge)
en < 1 h sur 8 Go, avec un seul fichier de config ; export GGUF déployable localement.

**Faiblesses / risques** : Soup est jeune (~72★, beaucoup de features BETA) — vérifier la stabilité ;
CPU « très lent » (réservé au test) ; qualité dépend surtout des données, pas de l'outil.

**Contraintes** : local (8 Go VRAM idéal ; CPU pour smoke test) ; données réelles d'un catalogue.

**Source** : `../Inspirations/llm-training.md` (Soup) ; `../Inspirations/agentic.md` (Colab CLI, comparaison) ;
`../Experiments/exp_distill_slm_bibliothecaire.md` (recette voisine) ; `../Experiments/exp_qat_fp4_slm_biblio.md`.
