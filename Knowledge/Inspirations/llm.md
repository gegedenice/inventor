# LLM/SLM/VLM architecture

## VLM trained without image encoder

Why is it interesting?

### Resources

- https://huggingface.co/spaces/HuggingFaceM4/encoder-free-vlm#trying-an-even-bigger-decoder-and-different-data

### Takeaway

### Questions

- Can the approach (manage VLM embeddings layer) be generalized to LLM to suit other goals (introduce knowledge, encode skills..)?

---

## World models

Why is it interesting?
- Predicting state rather than token

### Resources

- Nvidia Cosmo3 models (open platform of World Foundation Models (WFMs), training recipes, optimization practices, and data curation tools — purpose-built to understand, 
  simulate, and reason about the physical world for robotics, autonomous vehicles, and physical AI.): 
  for example https://huggingface.co/nvidia/Cosmos3-Nano 
- Qwen Agent world (paper+model+benchmark): https://huggingface.co/collections/Qwen/qwen-agentworld
- Jepa world models (Meta): https://github.com/facebookresearch/jepa-wms

### Takeaway

- [Cosmo3 models] "Built on a Mixture-of-Transformers (MoT) architecture, a single model pairs an autoregressive reasoner with a diffusion generator — reasoning before it generates — to unify vision reasoning, world generation, and action prediction across text, image, video, ambient sound, and action in one forward pass."
- [Qwen Agente world model]: "A world model predicts environment dynamics based on current observations and actions, serving as a core cognitive mechanism for reasoning and planning. 
  In this work, we investigate how world modeling based on language models can further push the boundaries of general agents"

### Questions

- Can world models (with concept of states) be generalized/applied to librarians workflows? 
- or for agent memory management?

### Random Connections

---

## SLM pre-training pipeline

Why is it interesting?
- Very complete synthesis
- including synthtic data generation

### Resources

- https://drive.google.com/file/d/1yWmT2Jz_6kn5xcZVOl_gzXVqPoE-vnlY/view

### Takeaway


### Questions


### Random Connections

---

## SLM extraction de propositions atomiques

### Resources

- SLM: https://huggingface.co/Zual/MPropositionneur-V2
- Paper: https://hal.science/hal-05597666v1

### Takeaway

"Qu'est-ce qu'une proposition atomique ? La notion est partout en NLP (FActScore, Dense X retrieval...), mais sans définition formelle consensuelle.
Dans notre article accepté à CORIA (CORIA-TALN 2026), nous comblons ce vide en deux temps :
- Une base formelle : En nous appuyant sur la théorie de l'information sémantique de Carnap et Bar-Hillel (1953), nous définissons ce qui est sécable ou non dans la sémantique d'un texte.
- Un modèle frugal (MPropositionneur-V2) : distillé de Qwen3-72B vers 0.6B. Il surpasse l'état de l'art (Chen et al. 2024) avec un format plus léger et multilingue.
Les résultats ? Des performances améliorées en extraction de triplets, en recherche d'information et en évaluation de résumé."

### Questions

- Index creation on multiples docs?
- Synthetize publications/thesis abstracts?

### Random Connections

- Karpathy LLM-wiki

---

## HuggingFace ecosystem

Why is it interesting?
- Remote inference or fine-tuning
- Agentic management (hf cli no installation `uvx hf...`, `hf skills add`...)

### Resources

- Jobs serving with hf cli (temporary endpoints): https://huggingface.co/docs/hub/jobs-serving
- Permanent endpoints: https://huggingface.co/docs/inference-endpoints/index
- Agent local with PI and llama.cpp (can be replaced by jobs serving or endpoint): https://huggingface.co/docs/hub/agents-local
- hf-mount: https://github.com/huggingface/hf-mount

### Takeaway


### Questions


### Random Connections

---