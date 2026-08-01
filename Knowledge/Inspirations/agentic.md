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

Spécification ouverte (Apache-2.0, Google Cloud Data Cloud — Sam McVeety, Amir Hormati —
annoncée juin 2026) : le contexte métier d'une organisation représenté comme un dossier de
fichiers Markdown à frontmatter YAML, liés comme un wiki. Un seul champ obligatoire : `type`.
C'est un format, pas une plateforme ni un runtime.

Why is it interesting?
- Open standardized format, sans SDK ni compte propriétaire : tout outil qui lit du texte lit un bundle OKF
- Markdown = lisible par un humain (un·e analyste édite sans plateforme) ET parsable machine, sans scraping ni fine-tuning
- Git-native : la connaissance reçoit PR, review, historique — « knowledge as code, not knowledge as archive »
- Déplace la question : de « comment entraîner le modèle sur notre savoir ? » à « comment maintenir notre savoir pour que n'importe quel modèle le comprenne ? »
- Passe du RAG (fragments récupérés) à un agent qui *hérite* d'une compréhension : relations, propriété, historique — pas seulement des chunks

### Resources

- https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing?hl=en
- POC: https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf
- Analyse critique (R. Rahmat, Google Cloud Community, 2026-07): https://medium.com/google-cloud/open-knowledge-format-the-missing-layer-for-context-aware-ai-agents-3694b1c1df1c

### Takeaway

"Google’s Open Knowledge Format makes curated business context portable: Markdown concepts, metadata, links, indexes and logs in a bundle agents can consume. 
Useful for data teams, agent builders and domain experts who need governed context outside prompts, websites or vector stores. 
The value is practical: inspectable knowledge, version control, review, reuse and cleaner handoff between systems."

### Questions

- Metadata in OKF in replacement of API or dump?
- Graph in OKF format better for agent than complicated graph, SparQL, Shacl.. queries?
- Fraîcheur : qui possède `weekly_active_users.md` après une réorg ? Les fichiers ne se mettent pas à jour seuls (le vrai test est la gouvernance, pas le format)
- Autorité : si deux équipes publient des bundles contradictoires sur la même métrique, lequel l'agent croit-il ?
- Passage à l'échelle : quelques centaines de fichiers = un wiki ; quelques millions = un problème de data engineering sans outillage mûr
- Adoption hors Google : devient-il un standard multi-éditeur seulement si d'autres *produisent* au format, pas seulement consomment ?

### Random Connections

- Karpathy LLM-wiki (cf. kb.md → `Papers/karpathy_llm-wiki.md`) : l'article cite le « LLM wiki » de Karpathy comme précurseur informel qu'OKF nomme et spécifie
- QMD dans kb.md : moteur de recherche local sur markdown — brique de *consommation* plausible d'un bundle OKF
- Cette base `Knowledge/` est elle-même un bundle quasi-OKF (markdown + frontmatter, `index.md`, `log.md`) → le repo Inventor comme dogfooding d'OKF
- Le pattern `AGENTS.md` (cité dans l'article aux côtés des vaults Obsidian) : l'`AGENTS.md` d'Inventor en est une instance

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

---

## AgentENV — environnement d'exécution pour l'Agentic RL à grande échelle

Infrastructure open-source (Tsinghua MADSys Lab + Moonshot AI, juillet 2026) qui donne à chaque agent en apprentissage par renforcement un microVM Firecracker fortement isolé, avec snapshots incrémentaux, forks copy-on-write et réutilisation mémoire/stockage. Utilisée pour l'entraînement de modèles avancés dont Kimi K3.

Why is it interesting?
- Fork copy-on-write d'un état intermédiaire → N environnements indépendants pour explorer plusieurs trajectoires en parallèle (multi-trajectory sampling, tree search) : le pattern d'échantillonnage vaut même hors microVM.
- Environnements inactifs rendus quasi-gratuits : pause/resume en dizaines de ms, libération CPU/mémoire pendant l'attente d'inférence — « le coût suit l'usage réel, pas le nombre d'environnements créés ».
- Déduplication par couches read-only partagées (OverlayBD, content-addressed) + snapshot mémoire partagé entre env issus du même template — seuls les écritures incrémentales sont privées.
- Observation utile : les agents pilotés par récompense trichent (sortent du bac à sable, modifient la logique d'évaluation, vont chercher les réponses) — l'isolation forte n'est pas un luxe mais une condition d'un signal d'entraînement propre.

### Resources

- https://kvcache.ai/blog/agentenv-open-sourced
- Code : https://github.com/kvcache-ai/AgentENV

### Takeaway

"The same intermediate state can also be forked into multiple independent execution environments using copy-on-write, allowing an agent to try different tool calls, code changes, or operation sequences in parallel. This provides an efficient environment foundation for multi-trajectory sampling and tree search."

### Questions

- Le fork COW pour explorer plusieurs stratégies en parallèle : réplicable à petite échelle (un agent bibliothécaire testant plusieurs pistes de catalogage) sans microVM, via processus ou git worktrees ?
- Pause/resume d'environnements inactifs : pattern transférable à la gestion de mémoire d'agent (cf. World models / Memory Caching dans llm.md) ?
- Le reward hacking observé ici : quel signal pour concevoir des évaluations robustes de tâches biblio (l'agent ne doit pas « gagner » en contournant la tâche) ?

### Random Connections

- SynthTraces (synthetic_data.md) : AgentENV est l'infra qui *exécute* et collecte des trajectoires d'agent à grande échelle ; le fork multi-trajectoires est la version industrielle du harnais qui génère des traces synthétiques.
- PI agents (ce fichier) : le harnais minimal est ce qui *tourne à l'intérieur* de ces environnements isolés.
- autoarxiv (misc.md) : reproduction minimale de papier en environnement isolé — AgentENV en est le cousin lourd (vrais environnements d'exécution, à l'échelle du cluster).

---

## PRINCE — agentic RAG fiable en production (Bayer × Thoughtworks)

Étude de cas Martin Fowler : un assistant de recherche agentique (RAG + Text-to-SQL) qui interroge des décennies de rapports d'études précliniques (PDF souvent scannés) chez Bayer. Relu sous deux angles : *context engineering* (ce que chaque agent voit, et ne voit pas) et *harness engineering* (l'échafaudage autour du modèle : orchestration LangGraph, reprise, observabilité, revue humaine).

Why is it interesting?
- Trajectoire **Search → Ask → Do** : des mots-clés/filtres au « ask » (RAG) puis au « do » (multi-agents rédigeant des documents réglementaires). Feuille de route transposable à un portail patrimonial / SIGB.
- **Context discipline** : des fenêtres plus grandes n'ont PAS supprimé le besoin de trier ; chaque étape reçoit un contexte distinct (planning / retrieval / evidence / synthesis) pour limiter la pollution de contexte et rester évaluable.
- **Fiabilité par le harnais, pas par le modèle** : persistance d'état (reprise au nœud échoué, pas de redémarrage complet), retries multi-niveaux, fallback inter-fournisseurs, agents nourris du contexte de l'erreur.
- **Confiance par citations granulaires** : survol d'une phrase → source + numéro de page + citation exacte du rapport ; les étapes intermédiaires (requêtes, outils) sont affichées. Exactement l'exigence de traçabilité d'une bibliothèque.
- Étape **Think & Plan** (process reflection) : évalue si la *trajectoire* progresse vers le but, distincte de la validation des *données* (Reflection agent) — améliore nettement la sélection d'outils quand ils se multiplient.
- **Recherche hiérarchique** : passage d'un Researcher monolithique à des sous-agents par domaine (chacun ses outils, schémas, sources d'autorité) pour éviter les fuites inter-domaines.

### Resources

- https://martinfowler.com/articles/reliable-llm-bayer.html
- fulltext in @../Papers/martinfowler_reliable-agentic-ai.md
- Paper académique (Frontiers in AI) : https://www.frontiersin.org/journals/artificial-intelligence/articles/10.3389/frai.2025.1636809/full

### Takeaway

"production-ready agentic AI is not only about better models or better prompts. Reliability comes from engineering both the context the model sees and the harness within which the model acts."

### Questions

- Text-to-SQL sur métadonnées structurées + RAG sur le PDF « gold standard » quand la métadonnée est lacunaire : patron directement applicable à un catalogue (notice incomplète) + document numérisé faisant foi ?
- Les citations granulaires (source + page + extrait exact) sont-elles réplicables frugalement sur un corpus patrimonial pour garantir la vérifiabilité d'une réponse ?
- « Context discipline » côté agent = pendant de « features simples > tout mettre dans le prompt » (cf. Deja, kb.md) ?

### Random Connections

- AgentENV (ce fichier) : PRINCE réclame états persistants + reprises ; AgentENV industrialise ce cycle état → exécution → snapshot → reprise (COW forks pour trajectoires alternatives).
- OKF (ce fichier) / Karpathy LLM-wiki (kb.md) : le « context engineering » rejoint « maintenir un savoir que le modèle comprend » ; les citations vers le doc source = la couche raw immuable.
- Idées « RAG sans embeddings » et « autocomplétion sans IA » (Ideas/ideas_2026-08-01.md) : PRINCE est le contre-exemple *lourd* (OpenSearch vectoriel, multi-agents) — utile pour cadrer quand la frugalité suffit vs quand le harnais complet se justifie (domaine régulé).
- Section NER/annotation de PRINCE : améliore la qualité des données en amont du RAG — rejoint MPropositionneur / claim-delta (llm.md).

---

## Orca — cockpit d'agents parallèles (fan-out puis « pick the winner »)

Orca (Stably AI, YC W22, open-source) est un « ADE » (Agent Development Environment) : une application desktop/mobile qui orchestre une flotte d'agents de code en parallèle, chacun isolé dans son propre git worktree, puis laisse comparer les résultats côte à côte et fusionner le meilleur. Supporte 30+ agents (Claude Code, Codex, Grok, Pi…) avec l'abonnement de l'utilisateur. ~31k étoiles en 4 mois.

Why is it interesting?
- Le pattern clé : *fan-out* d'un même prompt vers N agents en worktrees isolés → comparaison côte à côte → merge du gagnant. Vérification par mise en concurrence, pas par un seul essai.
- Orca ne remplace pas les agents : c'est une couche d'orchestration (isolation, monitoring, review, merge). Sépare « le cerveau » (les agents) de « la salle de contrôle » (Orca).
- L'orchestration sort du terminal : compagnon mobile, contrôle à distance (voix, téléphone), revues de dépôt planifiées.
- Git worktree comme isolation légère (vs microVM d'AgentENV) — exactement la piste « COW fork à petite échelle via git worktrees » notée le 2026-07-31.

### Resources

- https://githubdaily.medium.com/my-experience-migrating-from-cmux-to-orca-a-powerful-tool-for-multi-agent-parallel-verification-37e662b8c2c5 (récit de migration, member-only)
- Repo : https://github.com/stablyai/orca
- Product Hunt : https://www.producthunt.com/products/orca-5

### Takeaway

"Orca is the ADE for working with a fleet of parallel agents. Run any coding agent with your own subscription. Available on desktop, mobile and VPS."

### Questions

- Le terminal n'est pas une interface pour bibliothécaires — peut-on répliquer la *logique* d'Orca (fan-out → comparer → garder le meilleur) dans une UI grand public (cartes, pas de shell) pour des tâches biblio (catalogage, enrichissement, rédaction de réponses) ?
- Question plus large : à quoi ressemble l'« interface IA ultime » pour bibliothécaires / chercheurs / gouvernance universitaire — un cockpit d'agents parallèles adapté aux workflows documentaires ?
- La mise en concurrence de plusieurs agents comme mécanisme de qualité : transposable à un contrôle de notices (N agents, garder le consensus, signaler les désaccords) ?

### Random Connections

- AgentENV (ce fichier) : même « fan-out puis choisir » ; Orca l'isole par git worktree (léger, desktop), AgentENV par microVM copy-on-write (lourd, cluster).
- Idée « COW fork à petite échelle via git worktrees » (Ideas/ideas_2026-07-31.md) : Orca est cette idée productisée.
- PRINCE (ce fichier) : la « Reflection » interne y vérifie une trajectoire ; Orca externalise la vérification en mettant des agents en concurrence + revue humaine.
- PI agents (ce fichier) : Orca sait exécuter Pi parmi ses 30+ agents.

---

## SkillOpt — entraîner le skill (le texte), pas les poids

Optimiseur « text-space » de Microsoft (MIT, ~13k étoiles, arXiv 2605.23904) qui traite un *document de skill* en langage naturel comme l'état entraînable d'un agent LLM *gelé* : il l'améliore avec la discipline d'un optimiseur de deep learning (epochs, mini-batch, learning rate, portes de validation), sans jamais toucher aux poids. Livrable : un `best_skill.md` compact (300–2000 tokens) déployé tel quel sur le modèle inchangé.

Why is it interesting?
- Renverse « améliorer un agent = fine-tuner les poids » → « optimiser le *document de skill* avec rigueur ». Le savoir-faire reste en langage naturel : inspectable, versionnable, gouvernable.
- Anti-régression rare pour du prompt-engineering : edits bornés add/delete/replace produits par un modèle-optimiseur, acceptés *seulement* s'ils améliorent strictement un score de validation held-out ; buffer d'edits rejetés + update lent/méta par epoch.
- `best_skill.md` = artefact déployable qui tourne sur le modèle inchangé, **zéro appel d'inférence supplémentaire** au déploiement ; les skills transfèrent entre tailles de modèles et entre harnais (Codex ↔ Claude Code).
- **SkillOpt-Sleep** : auto-évolution nocturne hors-ligne (récolte → mine → rejoue → consolide derrière une porte de validation) — l'esprit « Lint/consolidation » d'un wiki d'agent, discipliné.

### Resources

- https://github.com/microsoft/SkillOpt
- Paper (arXiv) : https://arxiv.org/abs/2605.23904
- Project page : https://microsoft.github.io/SkillOpt/

### Takeaway

"Train agent skills like you train neural networks — with epochs, (mini-)batchsize, learning rates, and validation gates — but without touching model weights."

### Questions

- Entraîner un `skill.md` de catalogage / référence (UNIMARC, RAMEAU, réponse de service public) avec une porte de validation sur un jeu de notices étalon, puis le figer et le déployer — sans fine-tuning ?
- La porte de validation held-out (n'accepter un edit que s'il améliore le score) : à appliquer à nos propres skills Inventor (ingest, ideas) et à `AGENTS.md` pour éviter les régressions ?
- SkillOpt-Sleep (consolidation nocturne) : le brancher sur `Knowledge/` pour faire évoluer skills + wiki la nuit (rejoint l'idée « Lint frugal ») ?

### Random Connections

- Steering (llm.md → `Papers/iaetbibliotheques_steering.md`) : deux voies « contrôler sans fine-tuner » — steering = arithmétique de vecteurs d'activation, SkillOpt = optimisation d'un document texte. L'idée « vecteurs de steering comme compétences » (Ideas/ideas_2026-07-31.md) est la version *vecteur* de la même intuition ; SkillOpt en est la version *texte*.
- Karpathy LLM-wiki (kb.md) : SkillOpt-Sleep « harvest → consolidate behind a validation gate » = le Lint/consolidation du wiki, outillé.
- OKF (ce fichier) : un `best_skill.md` versionné et gouverné est un artefact quasi-OKF (markdown déployable, inspectable).
- PI packages / Orca / OpenClaw (ce fichier) : SkillOpt fournit des intégrations de harnais — skill entraîné puis déployé dans l'agent.

---

## OpenSpace — skills auto-évolutifs + communauté de partage

Framework HKUDS (MIT, ~6,6k étoiles) pour rendre des agents « plus malins, moins coûteux, auto-évolutifs » : un moteur de skills qui, après chaque exécution, analyse la trajectoire et fait évoluer les compétences, les stocke avec un DAG de versions et des métriques de qualité, et permet de les partager via une *cloud skill community*.

Why is it interesting?
- Évolution des skills par **3 déclencheurs explicites** : FIX (réparer un skill qui a échoué), DERIVED (en dériver un nouveau), CAPTURED (capturer une procédure réussie) — analysé automatiquement après exécution.
- Skills stockés avec **DAG de versions + métriques de qualité** (SQLite) : lignée et qualité tracées, pas un simple dossier de prompts.
- **Cloud Skill Community** : upload/download/recherche de skills entre agents (« Agents Experience Sharing »), recherche hybride BM25+embedding — un marché de compétences mutualisées.
- Low-cost revendiqué (~46 % de tokens en moins) ; *Smart Tool RAG* (BM25+embedding+LLM) pour sélectionner les outils ; backends unifiés (shell, GUI Computer Use, MCP, web) ; showcase « My Daily Monitor » = 60+ skills évolués, zéro code humain.

### Resources

- https://github.com/HKUDS/OpenSpace
- Communauté : https://open-space.cloud/

### Takeaway

"OpenSpace: Make Your Agents: Smarter, Low-Cost, Self-Evolving"

### Questions

- Une communauté de skills partagés entre établissements : des bibliothèques pourraient-elles mutualiser des skills de catalogage/référence (upload/download, recherche hybride) au lieu que chacune réinvente ses prompts ?
- Les déclencheurs FIX/DERIVED/CAPTURED : pour qu'un agent bibliothécaire *capture* automatiquement une chaîne d'enrichissement réussie en skill réutilisable ?
- DAG de versions + métriques de qualité des skills : le chaînon manquant entre SkillOpt (entraînement discipliné) et notre base (Lint/consolidation) ?

### Random Connections

- SkillOpt (ce fichier) : même but « skills auto-évolutifs sans toucher aux poids ». SkillOpt = optimisation *disciplinée* d'un document unique derrière une porte de validation ; OpenSpace = évolution *multi-skills* (3 déclencheurs) + partage cloud + DAG de versions. Complémentaires.
- Karpathy LLM-wiki (kb.md) : « les bonnes réponses refilées dans le wiki » = CAPTURED ; le DAG de versions outille la maintenance cumulative.
- Markets of agents / PI packages (ce fichier) : la Cloud Skill Community est un marché de compétences partageables.
- Smart Tool RAG (BM25+embedding+LLM) : rejoint QMD (kb.md) et l'idée « RAG sans embeddings » (Ideas/ideas_2026-08-01).

---

## OpenWorker — un « coworker » local-first qui livre du travail fini

Agent open-source (Andrew Ng, MIT) qui vit sur le bureau et rend des *livrables* — un document abouti, une réponse Slack avec les chiffres, un agenda mis à jour, une boîte mail triée — pas juste du chat. Tourne sur la machine de l'utilisateur, bring-your-own-model (OpenAI, Anthropic, Gemini, Inkling, GLM, Kimi, Qwen… ou 100 % local via Ollama). Bâti sur aisuite.

Why is it interesting?
- **« Deliver finished work, not chat »** : la sortie est un fichier ou une action, pas une to-do — l'agent est réorienté vers le livrable.
- **Local-first + BYO-model** (jusqu'à Ollama entièrement local) : les données ne quittent la machine que via le modèle et les intégrations *choisis* — argument de souveraineté fort pour bibliothèques/universités.
- **Actions à approbation** : écritures, envois et commandes shell sont gated ; les runs non surveillés parquent leurs demandes dans une inbox au lieu d'agir seuls — garde-fou humain natif.
- 25+ connecteurs (GitHub, Slack, Jira, Notion, Linear, Outlook, Gmail, Google Calendar…) + terminal/fichiers + tout outil MCP ; **automations planifiées** (brief matinal, rapport hebdo, veille d'un canal).

### Resources

- https://github.com/andrewyng/openworker
- Site : https://openworker.com
- Socle : aisuite (API unifiée multi-provider + couche agents) — https://github.com/andrewyng/aisuite

### Takeaway

"OpenWorker is an open-source AI coworker that lives on your desktop and delivers finished work, not just chat: a polished document, a Slack reply with the numbers, an updated calendar, a triaged inbox."

### Questions

- Un coworker local-first pour bibliothécaires : livrer des *livrables* (rapport de pilotage, notice enrichie, réponse de référence) plutôt que du chat — quel jeu de connecteurs métier (SUDOC, HAL, SIGB, OpenAlex) faudrait-il ?
- Souveraineté : le local-first + Ollama suffit-il aux exigences RGPD / données patrimoniales des établissements ?
- Automations planifiées (brief matinal) : transposables à une veille documentaire / un tableau de pilotage quotidien (cf. claim-delta, OSINT) ?

### Random Connections

- Orca / OpenSpace / PI agents (ce fichier) : OpenWorker = coworker desktop orienté *livrable* ; Orca = cockpit dev multi-agent — deux réponses à l'idée « interface d'agents non-terminal » (Ideas/ideas_2026-08-01, passe 3).
- PRINCE (ce fichier) : les actions à approbation = human-in-the-loop, même exigence de contrôle en domaine sensible.
- aisuite ↔ pi-ai (ce fichier) / HuggingFace ecosystem (llm.md) : couches unifiées multi-provider.
- Question ouverte « interface IA ultime pour bibliothécaires » (OpenQuestions/open_questions_2026-08-01.md, passe 3) : OpenWorker en est un candidat concret, orienté livrable.
