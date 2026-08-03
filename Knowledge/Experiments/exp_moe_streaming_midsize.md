# Expérience : streaming d'experts MoE sur un modèle mid-size (débit CPU)

**Hypothèse** : la technique de kimi-k3-in-c / Colibri (partie dense résidente + experts routés
streamés du disque, matmul basse précision sans déquantization) donne un débit **bien meilleur**
sur un MoE *mid-size* que sur Kimi K3 (~33 s/token), parce que la partie résidente et le volume
d'experts streamés par token chutent avec la taille — au point de devenir utilisable en interactif,
pas seulement en batch.

**Protocole minimal** : prendre un MoE ouvert mid-size (ex. un ~30–100B, ou Inkling-Small 276B/12B) ;
mesurer tok/s CPU en faisant varier (a) la RAM allouée aux experts résidents/pinned, (b) la bande
passante disque (SSD vs NVMe). Comparer à la loi observée sur K3. Tracer tok/s vs (params actifs,
Go RAM).

**Métrique visée** : identifier le seuil (taille de MoE, Go RAM) où le débit passe sous ~1 s/token
sur CPU — la frontière « batch nocturne » → « interactif » pour une tâche biblio.

**Faiblesses / risques** : dépend fortement du ratio actif/total et de la bande passante disque ;
peu de MoE mid-size vraiment ouverts + quantifiés MXFP4 ; portage du kernel non trivial.

**Contraintes** : CPU only ; réutiliser Colibri (GLM-5.2) ou kimi-k3-in-c comme base de mesure.

**Source** : `../Inspirations/llm.md` (kimi-k3-in-c, Colibri, AirLLM) ; `../Papers/kimi3_architecture_efficiency.md` ;
idée « MoE × AirLLM » (`../Ideas/ideas_2026-08-01.md`, passe 2).
