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