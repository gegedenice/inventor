# Agents

## HugginFace moonbot

HuggingFace infrastructure with Elasticsearch logs and MongoDB -> PI agent -> Slack requests

Why is it interesting?
- Sessions stored in a HF bucket for long memory
- PI as agent framework

### Resources

- https://huggingface.co/blog/huggingface/moon-bot
- Other PI implementation: https://blog.maudet.cloud/blog/souverain-et-open-source-modele-35b-ornith-pi/

### Takeaway

"Moon Bot — a Slack bot powered by the Pi coding agent SDK, running in a Kubernetes pod with privileged internal network access, and using HuggingFace Buckets as its persistent memory store."

### Questions

- Generating multiple small PI agents as infrastructure: is it a valuable service?

### Random Connections

---

## Markets of agents

### Resources

- Exchange market for agents: https://ai4trade.ai/
- Open Conference of AI Agents: https://agents4science.stanford.edu/

### Takeaway

### Questions

- Can it be duplicated for libarian tasks?

### Random Connections

## Frameworks

### Resources

- ClawCodex production-oriented Python rebuild of Claude Code: https://github.com/agentforce314/clawcodex
- mini-swe agent: https://github.com/SWE-agent/mini-swe-agent

### Takeaway

### Questions

### Random Connections

---

## MCP andre.vote

André l'agent IA de sociologie électorale comme connecteur MCP

Why is it interesting?
- Présentation de la page du MPC (https://mcp.andre.vote/), simple et précis

### Resources

- https://mcp.andre.vote/

### Takeaway

### Questions

### Random Connections

---

## OKF: Open Knowledge Format

Why is it interesting?
- Open standardized format

### Resources

- https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing?hl=en
- POC: https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf

### Takeaway

"Google’s Open Knowledge Format makes curated business context portable: Markdown concepts, metadata, links, indexes and logs in a bundle agents can consume. 
Useful for data teams, agent builders and domain experts who need governed context outside prompts, websites or vector stores. 
The value is practical: inspectable knowledge, version control, review, reuse and cleaner handoff between systems."

### Questions

- Metadata in OKF in replacement of API or dump?
- Graph in OKF format better for agent than complicated graph, SparQL, Shacl.. queries?

### Random Connections

- Karpathy LLM-wiki
- QMD - Query Markup Documents

---

## Colab CLI

Why is it interesting?
- agent full delegation of fine-tuning
- agent full delegation in general (all that can be done in Google Colab T4)

### Resources

- https://github.com/googlecolab/google-colab-cli

### Takeaway

"How to fine-tune a model for free from one prompt, with TRL and the Google Colab CLI:
- prompt: `You're in the TRL repo. Read the SFT examples in examples/scripts/ to learn the project's conventions, then adapt them into a small, self-contained training script for this task: fine-tune Qwen/Qwen2.5-0.5B-Instruct with QLoRA on philschmid/gretel-synthetic-text-to-sql (format schema + question -> SQL as chat messages). Run it on a remote Colab T4 via the Google Colab CLI: provision the GPU, install deps, log in to Hugging Face on the runtime, run a short demo run, stream metrics to a trackio Space, push the trained adapter to the Hub, and tear the session down. Report the final loss and the model URL.`
- Install the Colab CLI: uv tool install google-colab-cli (its agent skill ships with it).
- Run any colab command once to authorize Colab (it opens a Google sign-in in your browser).
- Log in to Hugging Face with a write token: hf auth login (so the run can stream to a trackio Space and push the model).
- Open your coding agent in a checkout of the TRL repo, paste the prompt, and watch it go (swap in any model or dataset you like)."

### Questions

### Random Connections

- hf CLI

---

## Flue - Open Agent framework

Why is it interesting?
- PI harness one again

### Resources

- https://github.com/withastro/flue

### Takeaway

"Flue builds on Pi’s minimal harness core and turns that pattern into a programmable TypeScript framework. 
You can define agents/workflows, attach skills and tools, run them in virtual/local/external sandboxes, 
and deploy across Node, Cloudflare, CI, or other runtimes"

### Questions

### Random Connections

---

## PI agents

Three minimal agent harness:
- @earendil-works/pi-coding-agent: Interactive coding agent CLI
- @earendil-works/pi-agent-core: Agent runtime with tool calling and state management
- @earendil-works/pi-ai: Unified multi-provider LLM API (OpenAI, Anthropic, Google, …)

Why is it interesting?
- Simplicity, atomicity, PI agents can be bundled as package

### Resources

- https://github.com/earendil-works/pi
- Project website: https://pi.dev/

### Takeaway

"Pi is a minimal agent harness. Adapt Pi to your workflows, not the other way around. Customize Pi with extensions, skills, prompt templates, and themes. Bundle them as Pi packages and share via npm or git.
Pi ships with powerful defaults but skips features like sub-agents and plan mode. Ask Pi to build what you want, or install a package that does it your way.
Four modes: interactive, print/JSON, RPC, and SDK. See OpenClaw for a real-world integration."

### Questions

- Extensible: possible to develop specialized PI agents fot library?

### Random Connections
