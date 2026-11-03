# LLM pre-training mid-training post-training

## Inkling-Small

Modèle open-weights de Thinking Machines Lab (30 juil. 2026) : un transformer Mixture-of-Experts de 276B paramètres totaux / 12B actifs, entraîné sur NVIDIA GB300 NVL72, qui égale le grand Inkling (975B/41B) à un quart de la taille. Multimodal natif (audio + images), effort de raisonnement variable (minimal→xhigh), fenêtre jusqu'à 1M tokens. Fine-tunable sur Tinker.

Why is it interesting?
- Benchmarks radar design
- Training process
- Recette « petit qui dépasse le grand » : post-entraînement par *on-policy distillation* avec Inkling comme teacher, puis ~2 semaines de RL de coding agentique — le petit surpasse le grand en raisonnement/agent, mais le grand garde l'avantage en connaissances/factualité (nuance honnête).
- *Variable thinking effort* : un même modèle balaie une courbe coût/performance (minimal→xhigh) — on choisit l'effort selon la tâche, pas un modèle différent.
- Multimodal *encoder-free* : audio en spectrogrammes dMel, images en patches 40×40 via un hMLP 4 couches, fusionnés légèrement avec les tokens texte — même famille que le « VLM sans encodeur » de llm.md.
- Épistémique : calibration par RL contre des règles de scoring propres (Brier) sur des questions de prévision réelles → confiance calibrée, utile pour des réponses vérifiables.

### Resources

- https://thinkingmachines.ai/news/inkling-small/
- Model card : https://thinkingmachines.ai/model-card/inkling-small/
- Poids (Hugging Face) : https://huggingface.co/thinkingmachines/Inkling-Small

### Takeaway

"we post-trained an earlier checkpoint, Inkling-Small (preview), in part using on-policy distillation with Inkling as the teacher. Starting from that checkpoint, we continued scaling agentic coding RL for two weeks. With these improvements, Inkling-Small surpassed Inkling on reasoning and agentic coding benchmarks. Inkling maintains an advantage on knowledge coverage and factuality."

### Questions

- On-policy distillation (grand teacher → petit) + RL : reproductible frugalement pour distiller un SLM « bibliothécaire » depuis un grand modèle (cf. MPropositionneur, 72B→0.6B) ?
- Le *thinking effort* variable : peut-on exposer un curseur d'effort au bibliothécaire (minimal pour du catalogage routinier, xhigh pour une question de recherche) — à la manière du curseur de flou de Deja ?
- Encoder-free multimodal (dMel + patches) : applicable à la description de documents patrimoniaux (image + texte) sans pipeline OCR lourd ?

### Random Connections

- VLM sans encodeur (llm.md) : même architecture encoder-free multimodale, mise à l'échelle ici (dMel audio + patches image).
- SLM extraction de propositions atomiques / MPropositionneur (llm.md) : autre distillation grand→petit (72B→0.6B) ; Inkling-Small confirme le patron on-policy distillation.
- Curseur de flou de Deja (kb.md) + idée « curseur recall/precision » (Ideas/ideas_2026-08-01.md) : le *variable thinking effort* est le même geste — exposer un cran de compromis coût/qualité.
- « Benchmarks radar design » → dataviz.md (graphes radar pour comparer des modèles).
- Tinker (fine-tuning hébergé) rejoint Colab CLI (agentic.md) et HuggingFace ecosystem (llm.md) — délégation frugale du fine-tuning.

---

## LLM from scratch

Why is it interesting?
- Demo in formation session
- Try a custom gpt2-style on ... ?

### Resources

- LLM Builder app https://languagemodelbuilder.com/: 90-minute interactive textbook covering the fundamentals (tokenization, embeddings, attention, the transformer, training data, loss and gradient descent, and fine-tuning).
  Then a training workbench that runs the full pipeline locally (Pre-train a base model on a dataset you pick, Fine-tune it with SFT, then align it with DPO, Watch the loss curve fall in real time as it learns,
  Chat with the model you made, with a token-probability X-ray view)
- Build a Small Language Model (SLM) From Scratch: Medium post https://medium.com/@shravankoninti/build-a-small-language-model-slm-from-scratch-3ddd13fa6470 and Colab https://colab.research.google.com/drive/1k4G3G5MxYLxawmPfAknUN7dbbmyqldQv?usp=sharing
- Train LLM from scratch: https://github.com/FareedKhan-dev/train-llm-from-scratch
- Build LLM from scratch (multiple colab notebooks): https://github.com/HayatoHongo/EveryonesLLM

### Takeaway

### Questions

### Random Connections

---

---

## Soup — fine-tuning / post-training LLM en une commande

CLI open-source (Apache-2.0) qui ramène le fine-tuning et le post-training à « un YAML, une commande » : SFT, DPO/GRPO/PPO/KTO/ORPO/SimPO/IPO…, QLoRA, quantization auto, export GGUF/ONNX/AWQ, exécution locale (QLoRA sur son propre GPU ; CPU expérimental, très lent). « Zero SSH ».

Why is it interesting?
- Frugalité outillée : QLoRA local (7B sur 8 Go VRAM), batch/GPU/quantization auto, backends Unsloth/MLX, et une **pile d'efficacité mémoire** (activation offloading, gradient checkpointing, GaLore, ReLoRA, DeepSpeed/FSDP). C'est le **pendant *entraînement* du streaming d'inférence** (AirLLM/Colibri) : décharger/streamer en RAM/VRAM ce qui ne tient pas — « streaming in RAM/VRAM » côté train.
- Gouvernance RL : v0.71.26 « closed-loop reward-hacking auto-mitigation » — *détecte* le reward hacking en cours d'entraînement, relève β (KL), *rollback* au dernier checkpoint sain, early-stop en dernier recours ; mode `log_only` pour observer d'abord.
- Chaîne complète data → train → eval → serve : 100+ recettes de modèles, templates (chat, code, medical, reasoning, moe, longcontext…), `soup doctor`, export Ollama/llama.cpp.
- Runs CPU possibles pour tester (quantization auto-désactivée) — utile pour prototyper une tâche biblio sans GPU.

### Resources

- https://github.com/MakazhanAlpamys/Soup
- Site : https://trysoup.dev

### Takeaway

"Fine-tune and post-train LLMs in one command. No SSH, no config hell."

### Questions

- Soup comme outil *one-command* pour fine-tuner un SLM biblio (normalisation de vedettes, mots-clés) en QLoRA sur 8 Go / CPU — coût/qualité vs Colab CLI (fine-tuning délégué à un GPU distant) ?
- Le « reward-hacking auto-mitigation » (détecter → corriger → continuer) : transposable comme garde-fou à d'autres boucles biblio (évals de qualité, consensus multi-agent) ?
- « Streaming en RAM/VRAM pour l'entraînement » (activation offloading, GaLore) : jusqu'où repousse-t-il la taille entraînable sur matériel modeste ?

### Random Connections

- Colab CLI (agentic.md) : fine-tuning délégué à un GPU distant vs Soup local one-command — deux frugalités complémentaires.
- AirLLM / Colibri / kimi-k3-in-c (llm.md) : streaming *à l'inférence* ; Soup apporte l'offloading/streaming *à l'entraînement* — même trend « ne pas tout tenir en mémoire ».
- Inkling-Small (on-policy distillation) + QAT FP4 (llm-training.md, `../Papers/kimi3_architecture_efficiency.md`) : Soup outille QAT/DPO/distillation — la brique d'exécution de ces recettes.
- Idée « SLM bibliothécaire distillé » (`../Experiments/exp_distill_slm_bibliothecaire.md`) : Soup rend son lancement trivial.
- **Weight-Space Programming** (ce fichier) : l'autre bout du spectre — éditer quelques poids sans gradient pour *réparer* un modèle, là où Soup *entraîne*.

---

## Weight-Space Programming — un LLM lit, répare et écrit directement des poids de réseaux

Billet (MIT CSAIL : Rikiya Takehi, Jayden Teoh, Alex Zhang, Omar Khattab, 2 novembre 2026) où un LLM généraliste de pointe (« GPT-6 Astra », non décrit publiquement) raisonne en langage naturel sur des poids bruts pour identifier ce que fait un réseau, le réparer ou l'écrire de zéro — sans descente de gradient.

Why is it interesting?
- Alternative au post-training : au lieu d'entraîner, *éditer*. Sur Qwen3.5-0.8B, le LLM relie les boucles de génération à une direction dans les MLP des couches 15 et 19, modifie 131 072 poids (0,016 %) avec 50 forward passes : MATH-500 7,9 % → 48,1 %, GSM8K 13,8 % → 67,1 %, IFEval 21,0 % → 62,3 % ; réponses tronquées à la limite de tokens 93 % → 33 %.
- Lecture des poids : depuis 17 253 poids d'un MLP joueur, il nomme le jeu (4/4) ; il identifie la langue d'entraînement de petits transformers (7,16M) et retrouve leurs têtes de copie ; il ne repère un modèle sycophante qu'avec accès aux patterns d'attention (pas aux poids seuls).
- Écriture de zéro : des politiques MinAtar écrites en un coup battent parfois PPO entraîné (Breakout 517 vs 25 à 500M pas), pas toujours (Seaquest 53 vs 89).
- Résultat négatif utile (hypothèse des auteurs) : sur Gemma 3 270M, pas d'amélioration en maths (6,8 % → 7,7 %) — l'édition retire des obstacles, elle n'ajoute pas une capacité absente.

### Resources

- https://rikiyat.github.io/blog/weight-space-programming/
- Logs, données et scripts : https://github.com/RikiyaT/lm_understand-logs
- Modèle édité : https://huggingface.co/Qwen/Qwen3.5-0.8B

### Takeaway

"our neural network weights will soon be natively optimized by LLMs"

### Questions

- Un SLM biblio qui boucle ou dérive (normalisation de vedettes, indexation) : un gros LLM peut-il diagnostiquer et corriger la panne dans les poids plutôt que de relancer un fine-tuning ?
- Renversement : « ne retire que des obstacles » → l'édition de poids comme *réparation* bon marché d'un modèle déjà compétent, le fine-tuning restant nécessaire pour ajouter une compétence ?
- Reproductibilité : le LLM n'est pas public, les baselines PPO tiennent sur 3 à 10 seeds — que reste-t-il avec un modèle disponible (Claude, open-weights) ? Résultats « very early » selon les auteurs.

### Random Connections

- **Le Steering** (`../Papers/iaetbibliotheques_steering.md`) : même famille « modifier le comportement sans ré-entraîner » ; le steering agit sur les activations à l'inférence, ici l'édition est gravée dans les poids — et la boucle Qwen se ramène à *une direction*, comme un vecteur de steering.
- **Soup** (ce fichier) : fine-tuning one-command vs édition chirurgicale sans gradient — deux coûts et deux garanties très différents pour adapter un SLM.
- **« System One » / AnyJev** (`llm.md`) : lire l'état caché d'un modèle pour décider ; ici un LLM lit poids et activations pour diagnostiquer — l'interprétabilité comme outil d'ingénierie.
