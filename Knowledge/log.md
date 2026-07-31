# Log

Journal chronologique, append-only. Chaque entrée commence par `## [AAAA-MM-JJ] <type> | <sujet>`
pour rester grep-able : `grep "^## \[" log.md | tail -5` donne les 5 dernières.
Types : `ingest`, `query`, `lint`, `refactor`.

## [2026-07-31] refactor | Préparation de Knowledge pour git
Ajout de index.md (catalogue), log.md (ce fichier), .gitignore et .gitkeep dans les dossiers
vides (Syntheses, Ideas, Experiments, OpenQuestions, Resources) pour que git suive l'arborescence.
Ajout du skill `.skills/ingest/SKILL.md` (workflow « donne une URL, l'agent range »).

## [2026-07-31] ingest | Karpathy — LLM Wiki
Source : https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
Entrée existante (vide) dans Inspirations/kb.md enrichie au lieu d'être dupliquée ; fulltext
extrait dans Papers/karpathy_llm-wiki.md. Cross-links posés vers QMD, PageFind, Airweave.
