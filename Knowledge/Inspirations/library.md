# Library

## OpenAlex snapshot

Latest OpenAlex snapshot now ships in Parquet alongside the existing JSON Lines format.
Data now lives under format-specific paths:
- s3://openalex/data/parquet/<entity>/…
- s3://openalex/data/jsonl/<entity>/…

Why is it interesting?
- Parquet -> DuckDB, Polars, Spark, pandas
- smaller downloads and lower storage
- Skip data you don't need:  use built-in column statistics to skip whole chunks of files when filtering (e.g. by publication_year), instead of reading everything.

### Resources

- https://developers.openalex.org/download/snapshot-format

### Takeaway

### Questions

- Storage on HF bucket -> agent, dataviz?

### Random Connections

---

## Reading Research Papers in the age of LLMs

Medium blog post, fulltext in @../Papers/medium_reading-research-papers.md

Why is it interesting?
- Raccourcis url (replace an url section and move to an augmented page)
- Augmented reading techniques

### Resources

- https://developers.openalex.org/download/snapshot-format

### Takeaway

### Questions

- Generalize the url replacement system to custom features?
- Compact reading techniques in an AI agent or custom app?

### Random Connections

---

## OCR de 30 000 papiers par un agent (Codex + OCR ouvert + HF Jobs + hf-mount)

Retour d'expérience (Niels Rogge, Hugging Face, avril 2026) : convertir ~27 000 papiers arXiv sans version HTML en Markdown pour les rendre « chattables ». Un agent de code (Codex) écrit et pilote tout le pipeline — choix du GPU par mini-benchmarks de coût, lancement et suivi de 16 jobs parallèles, écriture dans un bucket monté. Modèle OCR **ouvert** Chandra-OCR-2 (Datalab, 5B), choisi via le leaderboard OlmOCR-bench, exécuté sur HF Jobs (GPU serverless, paiement à la seconde), résultats stockés en HF Buckets via hf-mount.

Why is it interesting?
- Numérisation de masse *déléguée à un agent* : Codex a écrit le script, comparé A10G/L40S sur 120 papiers, estimé le coût, lancé et monitoré 16 jobs — l'humain se contentait de « check the progress ». Transposable à l'OCR d'un fonds patrimonial.
- Frugalité chiffrée : ~850 $ (16× L40S, ~30 h) vs 1 841–2 761 $ via l'API du fournisseur — auto-héberger un modèle ouvert sur GPU serverless divise le coût.
- Choix du modèle par **leaderboard reproductible** (OlmOCR-bench) plutôt qu'au flair ; licence ouverte (OpenRAIL) → usage commercial possible.
- hf-mount + Buckets (Xet : mutable, non versionné git) : écrire dans un stockage objet *comme un disque local*, sans coder download/upload ; adapté à un corpus qui grossit chaque jour (les commits git exploseraient).

### Resources

- https://huggingface.co/blog/nielsr/ocr-papers-jobs
- Modèle : https://huggingface.co/datalab-to/chandra-ocr-2
- Benchmark : https://huggingface.co/datasets/allenai/olmOCR-bench
- Code : https://github.com/NielsRogge/arxiv-ocr

### Takeaway

"nowadays we can simply point a coding agent such as Claude Code, Cursor or Codex to a set of URLs and it will figure it out by itself."

### Questions

- OCR de masse d'un fonds patrimonial (PDF scannés, thèses, archives) délégué à un agent + modèle OCR ouvert + GPU serverless : quel couple coût/qualité pour un établissement ? (observation : un commentaire du billet juge le « chat with paper » de qualité médiocre — la qualité OCR reste à valider.)
- hf-mount + Buckets pour un corpus documentaire croissant : alternative au versioning git quand les commits exploseraient (rejoint l'idée AirLLM×HF-buckets) ?
- Chandra-OCR-2 (VLM 5B) exécutable frugalement (Colibri / AirLLM) sur CPU ou petit GPU pour de l'OCR souverain hors-ligne ?

### Random Connections

- HuggingFace ecosystem (llm.md) : hf-mount + Jobs y sont déjà notés — ici mis en œuvre à l'échelle.
- Idée « AirLLM × HF-buckets » (Ideas/ideas_2026-08-01.md, passe 2) : même brique hf-mount, autre usage (stockage de sorties vs shards de poids).
- autoarxiv (misc.md) : autre « papier → artefact » ; ici papier scanné → Markdown chattable.
- Colibri / AirLLM (llm.md) : faire tourner un VLM d'OCR frugalement.
- Reading Research Papers (ce fichier) : le Markdown de papier comme brique de lecture augmentée.

