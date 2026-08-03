# Questions ouvertes — passe 2026-08-03 (Kimi K3 : attention linéaire, latent, QAT)

- **Accès à l'état / au latent** : les runtimes (llama.cpp, vLLM, RWKV/Mamba) exposent-ils l'état
  récurrent et le latent MLA de façon à pouvoir y injecter/extraire un vecteur ? Sans cet accès, le
  steering d'état reste théorique. → idées « steering de l'état récurrent » et « latent LatentMoE/MLA ».

- **Steering par couche vs par état** : lequel tient le mieux un biais sur un long document, et à quel
  coût ? Y a-t-il un régime (contexte court/long) qui favorise l'un ou l'autre ? → idée steering état.

- **QAT natif à petite échelle** : à partir de quelle taille de modèle / quel budget le QAT (entraîner
  dans la précision de déploiement) bat-il nettement la quantization a posteriori pour une tâche biblio ?
  → `Experiments/exp_qat_fp4_slm_biblio.md`.

- **FP4 réel sur CPU** : le vrai FP4 est peu supporté hors GPU récents ; jusqu'où l'int4/int8 simulé
  reproduit-il le bénéfice du QAT natif pour un déploiement CPU souverain ? → posttraining.md, Colibri.

- **Hybride 3:1 (KDA + MLA) transposable petit ?** Le ratio « 3 couches rapides à état fixe + 1 couche
  de récupération exacte » a-t-il un sens à l'échelle SLM biblio (long contexte de notices/fonds) ? →
  inference_archi.md.
