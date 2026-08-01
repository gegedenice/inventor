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
