# Expérience : RAG sémantique frugal et local sur un fonds de notices (turbovec)

**Hypothèse** : un index vectoriel **turbovec** (TurboQuant, 2–4 bit, CPU-SIMD, air-gapped) rend la
recherche *sémantique* sur un fonds de notices/documents océrisés abordable sur matériel possédé —
16× de compression, pas d'étape d'entraînement, ingest en ligne — au point de concurrencer, voire
compléter, l'index lexical FTS5/BM25 (`exp_fts5_index_knowledge.md`). Le meilleur système est
probablement **hybride** : FTS5/BM25 (ou une allowlist SQL) filtre les candidats, turbovec fait le
rerank dense — turbovec honorant la liste d'ids *dans* le kernel, sans over-fetch.

**Protocole minimal** : (1) embarquer un corpus (nos `Knowledge/` en dogfood, ou un échantillon de
notices/plein-texte océrisé) avec un modèle d'embedding open-source local ; (2) indexer avec
`TurboQuantIndex`/`IdMapIndex` (d selon le modèle, bit_width 2 puis 4 ; `calibrate()` sur ~1024 vecteurs) ;
(3) mesurer rappel@k vs float32 exact et vs FTS5/BM25, RAM occupée, latence de recherche CPU,
temps d'ingest ; (4) tester le mode **hybride** (allowlist FTS5 → rerank turbovec) sur des requêtes
de type « trouver les notices proches de X » ou Q/R de catalogage.

**Métrique visée** : trouver le bit_width (2 vs 4) où rappel@10 ≈ float32 tout en tenant le corpus en
RAM modeste ; établir si l'hybride lexical→dense bat chacun pris seul sur des requêtes biblio réelles ;
chiffrer le coût mémoire par notice (octets/vecteur) pour dimensionner un déploiement d'établissement.

**Faiblesses / risques** : dépend de la qualité de l'embedding local (hors périmètre turbovec) ;
d=faible (type GloVe) = régime où TurboQuant décroche à 2-bit (calibration TQ+ requise) ; comparer
équitablement à FTS5 demande des requêtes annotées ; le gain sémantique doit *valoir* la brique
embedding vs un simple BM25.

**Contraintes** : CPU only, mémoire réduite, tout local/air-gapped (aucune donnée ne sort — RGPD) ;
`pip install turbovec` (Rust + bindings Python), intégrations LangChain/LlamaIndex/Haystack dispo.

**Source** : `../Inspirations/kb.md` (Turbovec + TurboQuant) ; https://github.com/RyanCodrai/turbovec ;
arXiv:2504.19874 ; à croiser avec `exp_fts5_index_knowledge.md` (lexical) et le weak signal
« pertinence sans vector DB » (`../WeakSignals/weak_signals_2026-08-01.md`) que cette expérience met à l'épreuve.
