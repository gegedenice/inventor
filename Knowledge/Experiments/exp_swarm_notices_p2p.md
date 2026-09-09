# Expérience : mutualiser le parc d'un établissement pour l'inférence (SwarmLLM P2P)

**Hypothèse** : pooler plusieurs postes hétérogènes d'une bibliothèque (onglets navigateur, WebGPU),
chacun tenant une tranche de couches reliée en WebRTC, fait tourner un modèle mid-size qu'aucun
poste ne tiendrait seul — et le débit agrégé rend l'enrichissement de notices nocturne viable
*sans* GPU serveur ni installation, avec une sortie **bit-exact** (MTP spéculatif gated golden tests).
C'est l'axe *pooling réseau* complémentaire du *streaming disque mono-machine* (Colibri/AirLLM).

**Protocole minimal** : (1) monter une room SwarmLLM sur 2–4 postes du même service (même Wi-Fi),
répartir Qwen 3.8 27B (ou un mid-size) ; (2) mesurer tok/s décodage plain vs spéculatif, temps de
prefill, et l'effet du nombre de devices / de la tranche par device ; (3) comparer au streaming
disque mono-machine sur la même tâche « texte → champs de notice » ; (4) tester une room
*cross-réseau* (deux sites) et mesurer la chute (le README annonce 3,5–6 tok/s).

**Métrique visée** : trouver la config (nb de postes, découpe des couches) où le débit agrégé passe
au-dessus du seuil « batch nocturne utile » sur du matériel *déjà présent*, et vérifier que le
prefill série (récurrence Gated-DeltaNet) ne domine pas le temps total sur des prompts de notice.

**Faiblesses / risques** : dépend de WebGPU (pas CPU-only — s'écarte de la contrainte principale de
la base, à assumer) ; prefill série jeune ; Safari recharge l'onglet sous pression mémoire s'il
tient une grosse tranche ; **confidentialité** : les activations mi-modèle sont lisibles par un pair
déterminé → n'a de sens qu'entre postes d'un même service de confiance, jamais en room ouverte (RGPD).

**Contraintes** : matériel possédé, navigateur WebGPU (Chrome/macOS testé) ; aucune installation ;
« rien ne quitte la pièce » mais pas de garantie de confidentialité inter-pairs.

**Source** : `../Inspirations/llm.md` (SwarmLLM) ; https://github.com/Nehanth/swarmllm ;
docs/architecture.md + docs/bench-log.md ; à comparer aux candidats streaming disque
(`exp_colibri_notices_nocturne.md`, `exp_airllm_shards_distants.md`).
