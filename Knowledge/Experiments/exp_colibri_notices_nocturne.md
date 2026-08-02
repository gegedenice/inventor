# Expérience : débit CPU-only de Colibri pour l'enrichissement de notices nocturne

**Hypothèse** : un gros MoE servi par Colibri sur CPU (matériel possédé, sans API) atteint un
débit suffisant pour enrichir un lot de notices pendant la nuit.
**Protocole minimal** : installer Colibri + container GLM-5.2 int4 ; mesurer les tok/s à chaud
et à froid sur une machine 25 Go puis ~128 Go RAM ; extrapoler le nombre de notices/nuit pour
un prompt d'enrichissement type (~300 tokens sortie).
**Métrique de décision** : notices traitables par nuit (8 h) ≥ seuil utile (à fixer selon le volume réel).
**Statut** : proposé.
**Source / fiche** : `../StateOfTheArt/inference_archi.md` · **Colibri** (`../Inspirations/llm.md`).
