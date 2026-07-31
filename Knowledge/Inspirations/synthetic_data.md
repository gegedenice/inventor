# Synthetic data

## SynthTraces

Why is it interesting?
- Synthetic data by agentic harness

### Resources

- https://github.com/julien-c/synthtraces
- https://huggingface.co/datasets/julien-c/synthtraces

### Takeaway

"A minimal codebase to generate synthetic coding agent session traces using Pi.
I wanted synthetic coding-agent traces, so I built a tiny harness where two models talk to each other:
- an open model (served via HF Inference Providers) plays the coding agent — it gets read + bash access to a real open source codebase (the huggingface OSS projects)
- a small local model (llama.cpp) plays the human user, asking simple questions like 'how do I run this?' or 'how is CI set up?'
The result is more than 2,000 Pi session traces which can be used to train or fine-tune LLMs and optimize them for the Pi harness"

### Questions

- PI harness as an army-knife?

---

## AutoData

An agentic data scientist to create high quality synthetic data

### Resources

- https://arxiv.org/abs/2606.25996