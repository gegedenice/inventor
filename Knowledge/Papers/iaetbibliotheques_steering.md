---
Title: Le Steering, ou comment comment modifier le comportement d’un LLM sans fine-tuning
URL Source: https://iaetbibliotheques.fr/2026/01/le-steering-ou-comment-modifier-le-comportement-dun-llm-sans-fine-tuning
Published Time: 2026-01-16T13:26:34+01:00
---

* * *

Code accessible [ici](https://github.com/gegedenice/iaetbibliotheques/tree/main/2026_01_steering)

* * *

Le Steering, ou Activation steering, consiste à orienter la sortie d’un LLM (la prédiction du token suivant) en **manipulant directement les représentations intermédiaires calculées par le modèle au moment de l’inférence** dans le but d’activer certains sous-espaces plutôt que d’autres.

Traditionnellement les techniques utilisées pour contrôler le comportement d’un modèle pré-entraîné invoquent soit le prompting soit le fine-tuning, c’est-à dire soit le guidage du modèle par l’apport de contexte, soit la modification de ses poids appris lors du pre-training. Comparativement, le steering se positionne comme une stratégie alternative (jusqu’à un certain point pour le fine-tuning, comme nous allons le voir par la suite), ou complémentaire, en tout cas assez fascinante, pour infléchir à la volée et de manière légère le comportement d’un modèle en se basant sur de l’arithmétique de vecteurs dans l’espace d’activation interne du réseau.

Comment faire du steering donc ? Prenons un LLM avec une architecture Transformer de type Llama classique, par exemple Llama-3.1-8B-Instruct . Posons-lui une question assez générale du type “Give me some ideas for starting a business”, voici la réponse du modèle pré-entraîné

```
Here are some business ideas across various industries:

**Online Businesses**
1. **E-commerce store**: Sell products through an online platform, such as Amazon or Shopify.
2. **Digital marketing agency**: Offer services like SEO, social media management, and content creation.
3. **Online course creation**: Teach others a skill or subject through platforms like Udemy or Skillshare.
4. **Affiliate marketing**: Promote products or services and earn a commission on sales.
5. **Blogging**: Create a website with valuable content and monetize it with advertising or sponsorships.
**Service-Based Businesses**
1. **Freelance writing
```

Imaginons que nous arrivions à isoler dans l’espace des représentations vectorielles du modèle (son espace latent) les coordonnées du vecteur représentant le concept de “library”, et que nous ajoutions ce vecteur par une simple addition à une couche intermédiaire du réseau durant l’inférence, voici le nouvel output généré à partir du même prompt

```
Here are some ideas for starting a library:

**Book-related services**
1. **Bookmobile**: A mobile service that brings books to communities, schools, and other institutions.
2. of the book: A community program where volunteers help patrons find books and offers reading programs for children and adults.
**Community spaces**
1. **Children's literacy center**: A space with resources, programs, and staff support to promote early literacy skills and literacy services for children.
2. **Literary cafe`: A quiet, peaceful space offering free access to books, media materials, and other resources.
**Digital services**
1. **E-book lending system**
```

Etonnant, non ?

Pour comprendre comment fonctionne précisément le steering, il faut par contre tout d'abord faire un détour par la notion d’espace latent, concept popularisé pour les LLMs par des équipes de Google à partir de 2022, ce qui implique aussi de rappeler brièvement comment sont entraînés les LLM et comment se déroule l’inférence.

## Anatomie d'un LLM

Pour illustrer le propos, nous prendrons comme exemple dans la suite de ce billet le modèle [Llama-3.2-3B-Instruct](https://huggingface.co/meta-llama/Llama-3.2-3B-Instruct) chargé à l’aide de la bibliothèque transformers d’HuggingFace.

**Voir le code**
```
import os, torch
from transformers import AutoModelForCausalLM, AutoTokenizer

MODEL_NAME = "meta-llama/Llama-3.2-3B-Instruct"
DEVICE = "cuda" if torch.cuda.is_available() else "cpu"
TORCH_DTYPE = torch.bfloat16 if torch.cuda.is_bf16_supported() else torch.float16

def load_model_tokenizer(base_model_name: str):
    tokenizer = AutoTokenizer.from_pretrained(base_model_name, use_fast=True)
    if tokenizer.pad_token is None:
        tokenizer.pad_token = tokenizer.eos_token
    model = AutoModelForCausalLM.from_pretrained(
        base_model_name,
        dtype=TORCH_DTYPE,
    ).to(DEVICE)

model,tokenizer = load_model_tokenizer(MODEL_NAME)
```

### Tokenisation

Tout prompt (chaîne de texte) passé en input au LLM est en premier lieu tokenisé, c’est‑à‑dire mappé sur le dictionnaire des _V_ tokens du vocabulaire (construit lors du pre-training) et converti en une séquence d’identifiants numériques correspondant à ces tokens.

**Inspecter le vocabulaire d'un modèle avec la commande tokenizer.get_vocab()**
```
{'Ġfestivities': 80459,
 'Ġunethical': 89735,
 'ĠDiscounts': 99474,
 'ĠRealty': 69114,
 '.lib': 15296,
 '::__': 19119,
 '.SYSTEM': 83624,
 'ÙĪÙĬØ±': 116237,
 'ĠlÃ´ng': 124240,
 'ropic': 45036,
 '(blocks': 98307,
 'fly': 22374,
 'Ġà¤īà¤¸à¤ķ': 102809,
 '_patterns': 66023,
 'ĠCeltics': 58796,
 'Ġ[];Ċ': 6032,
 'Ġdias': 41470,
 'ĠXt': 46553,
...
}
```

Une fois tokenisé, le prompt est donc représenté sous la forme d’un [tenseur](https://fr.wikipedia.org/wiki/Tenseur_%28math%C3%A9matiques%29) d’entiers de dimension (N,) où N est le nombre de tokens du prompt. Dans le cas d’un modèle Instruct, cette étape inclut également l’application d’un chat template (rôles system / user / assistant).

Par exemple avec notre modèle, un input de type

`message =[{"role": "system", "content": "You are a helpful assistant"},{"role": "user", "content": "What is latent space for LLM?"}]`

est reformaté avec les tokens spéciaux du modèle utilisé pendant l'entraînement

`<|begin_of_text|><|start_header_id|>system<|end_header_id|>You are a helpful assistant<|eot_id|><|start_header_id|>user<|end_header_id|>What is latent space for LLM?<|eot_id|><|start_header_id|>assistant<|end_header_id|>`

puis converti en séquence d'identifiants des tokens

`[128000, 128006,   9125, 128007,    271,  38766,   1303,  33025,   2696, 25,   6790,    220,   2366,     18,    198,  15724,   2696,     25,220,    845,   4448,    220,   2366,     21,    271,   2675,    527,264,  11190,  18328, 128009, 128006,    882, 128007,    271,   3923, 374,  42767,   3634,    369,    445,  11237,     30, 128009, 128006,78191, 128007,    271]`

_La longueur du prompt en tokens joue un rôle central car elle détermine la taille des matrices manipulées par le modèle à chaque étape de l’inférence._

**Manipuler prompt et tokens avec le tokenizer**
```
message = [{"role": "system", "content": "You are a helpful assistant"},
          {"role": "user", "content": "What is latent space for LLM?"}]
input = tokenizer.apply_chat_template(message, add_generation_prompt=True, tokenize=False)
input_ids = tokenizer.apply_chat_template(message, return_tensors="pt", tokenize=True, return_dict=True, add_generation_prompt=True).to(DEVICE)

print(input) 
# returns <|begin_of_text|><|start_header_id|>system<|end_header_id|>Cutting Knowledge Date: December 2023 Today Date: 16 Jan 2026 You are a helpful assistant<|eot_id|><|start_header_id|>user<|end_header_id|>What is latent space for LLM?<|eot_id|><|start_header_id|>assistant<|end_header_id|>

print(input_ids) 
# returns {'input_ids': tensor([[128000, 128006,   9125, 128007,    271,  38766,   1303,  33025,   2696,
             25,   6790,    220,   2366,     18,    198,  15724,   2696,     25,
            220,    845,   4448,    220,   2366,     21,    271,   2675,    527,
            264,  11190,  18328, 128009, 128006,    882, 128007,    271,   3923,
            374,  42767,   3634,    369,    445,  11237,     30, 128009, 128006,
          78191, 128007,    271]], device='cuda:0'), 'attention_mask': tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
         1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]],
       device='cuda:0')}

# Tokens number in prompt
len(input_ids["input_ids"][0]) # returns 48

# Last token
tokenizer.decode(input_ids["input_ids"][0][47]) # returns '\n\n'

# Reverse ids to tokens
tokens = tokenizer.convert_ids_to_tokens(input_ids['input_ids'][0])
# returns ['<|begin_of_text|>', '<|start_header_id|>', 'system', '<|end_header_id|>...]
# Reverse tokens to ids
ids = tokenizer.convert_tokens_to_ids(tokens)
# retruns [128000, 128006, 9125, 128007, 271, 38766, 1303,...]
```

### Formation des hidden states

Une architecture Transformer decoder-only est constituée d’une superposition de couches de neurones, et est auto-régressive, ou auto-récursive, puisqu'elle génère un token à la fois, chaque nouveau token étant conditionné par tous les précédents.

Durant le processus génératif, les tokens du prompt préalablement convertis en suite d'identifiants vont donc circuler de couche en couche à l'intérieur du réseau, et la toute première couche qu’ils rencontrent est une couche d’embeddings (formée durant l’apprentissage du modèle) qui contient des vecteurs de dimension _D_ (ici 3072) représentant chacun des 128 256 tokens du vocabulaire, ainsi que des embeddings positionnels nécessaires pour encoder l’ordre des tokens. Les _N_ tokens du prompt sont donc projetés dans un espace vectoriel continu de dimension _D_, de manière à être représentés à la sortie de cette couche par une matrice de dimension _N × D_.

Cette matrice traverse ensuite la succession des _x_ (28 ici) couches Transformer du modèle, en étant soumise à chaque étape à des processus calculatoires qui modifient et enrichissent contextuellement la représentation vectorielle de chaque token, et ce jusqu’à la dernière couche où se forme la prédiction du token suivant. En effet, chaque couche est composée principalement (mais pas uniquement) de deux blocs :

*   le [bloc d’attention](https://www.datacamp.com/fr/blog/attention-mechanism-in-llms-intuition), qui permet à chaque token d’intégrer des informations provenant des tokens précédents : ce bloc contient les poids appris du modèle, c’est-à-dire toutes les matrices qui encodent et pondèrent la relation sémantique de chaque token par rapport aux autres dans sa séquence, et qui permettent d’enrichir les représentations vectorielles des tokens du prompt avec les informations de son contexte (antérieur dans le cadre d’une attention causale).
*   le bloc [feed‑forward (MLP)](https://www.datacamp.com/tutorial/multilayer-perceptrons-in-machine-learning), qui projette temporairement les représentations dans un espace de plus grande dimension (ici 8192) afin d’y introduire de nouvelles directions et de la non-linéarité et d'y encoder chaque token dans un espace plus riche, constituant en quelque sorte une manière d'accroître la sémantique en dehors de l'attention.

_Remarque : cette description est une vue très simplifiée des couches d'un réseau Transformer, notamment car des opérations de normalisation et des connexions résiduelles s’intercalent également entre ces blocs._

* * *

Vous trouverez [sur ce site](https://poloclub.github.io/transformer-explainer/) une appli de visualisation interactive très sympa pour se représenter le cheminement des tokens dans le réseau de neurones

![Image 1: enter image description here](https://iaetbibliotheques.fr/content/images/transformer-explained.png)

* * *

Les vecteurs qui circulent ainsi de couche en couche dans le réseau sont appelés **hidden states**, et sont donc des représentations vectorielles intermédiaires, dynamiques, et dépendantes à la fois du prompt et des poids du modèle.

### Logits et prédiction du token suivant

Lorsqu’on exécute une inférence avec les options nécessaires dans le code (`out = model(**input_ids, output_hidden_states=True)`), on peut inspecter à la fois les les logits produits par la dernière couche et les hidden states de chaque couche.

![Image 2: enter image description here](https://iaetbibliotheques.fr/content/images/llm_output_logits_hidden_states.png)

Les logits que l’on voit dans les premières lignes de l’image çi-dessus représentent la génération finale du modèle, soit une matrice de dimension _N_ x _V_, où conformément à notre notation précédente _N_ est le nombre de tokens du prompt (48) et _V_ la taille du vocabulaire (128256). Dans cette matrice, chaque ligne associe à chaque token du prompt un vecteur de taille V de scores de compatibilité non normalisés pour tous les tokens du vocabulaire.

**Ces logits qui ne sont pas encore des probabilités le deviennent ensuite par application de la [fonction softmax](https://www.datacamp.com/fr/tutorial/softmax-activation-function-in-python) qui transforme ces scores en une distribution de probabilité sur le vocabulaire, à partir de laquelle le prochain token est effectivement sélectionné.**

Par construction, le modèle prédit donc un token supplémentaire pour chaque token du prompt, mais évidemment en pratique seule la dernière ligne des logits correspondant au dernier token du prompt nous intéresse et est utilisée pour produire le token suivant. Ce qui donne par exemple pour le dernier token du prompt précédent :

| index | Token ID in the vocab | Token in the vocab | Logit (score before softmax) |
| --- | --- | --- | --- |
| 644 | 644 | In | 25.25 |
| 24015 | 24015 | Lat | 24.625 |
| 334 | 334 | ** | 23.125 |
| 32 | 32 | A | 21.5 |
| 791 | 791 | The | 18.625 |
| 51123 | 51123 | latent | 17.875 |
| 42767 | 42767 | latent | 17.625 |
| 40 | 40 | I | 17.25 |
| 27266 | 27266 | _lat | 16.75 |
| 39111 | 39111 | Latitude | 16.75 |

**Voir le code pour inspecter les logits et la génération du token suivant**
```
out = model(**input_ids, output_hidden_states=True)

# Logits dimension
logits = out.logits[0]
logits.shape 
# returns torch.Size([48, 128256])

# See the logits for the last token
token_data = []
current_logits = logits[47] # last prompt token, set 9 for the 10th prompt token etc....
for token_id in range(len(current_logits)):
    token = tokenizer.decode([token_id])
    logit = current_logits[token_id].item()
    #probs = torch.nn.functional.softmax(current_logits, dim=-1)
    token_data.append((token_id, token, logit))

df = pd.DataFrame(token_data, columns=['Token ID in the vocab', 'Token in the vocab', 'Logit (score before softmax)'])
df.sort_values(by=''Logit (score before softmax)', ascending=False).head(10)
```

_A noter que c'est à cette étape, et après la fonction softmax, qu'interviennent les éventuels mécanismes de sampling comme la température, le top‑k ou le top‑p_

## Hidden states (espace d’activation) et espace latent

On en arrive enfin à notre espace latent, en observant dans l'image précédente les vecteurs constitutifs des hidden states entre chaque couche du modèle qui apparaissent en-dessous des logits : comme nous l'avons vu, ceux-ci correspondent aux représentations vectorielles intermédiaires des tokens, recalculées et contextuellement enrichies à chaque couche du réseau, mais qui peuvent aussi être vues comme **des coordonnées (des positions) dans l'espace latent du modèle dont la géométrie est définie par les poids appris lors de l’entraînement**.

Pour le dire autrement, la géométrie globale de l'espace latent du modèle, qui définit à la fois la structure conceptuelle du modèle et l'ensemble des transformations possibles, est entièrement induite par les poids du modèle, c'est-à dire toutes les matrices apprises lors de l’entraînement (réparties dans les blocs d’attention, les réseaux feed-forward et les couches d’embedding). Lors de la génération, les tokens du prompt sont projetés dans cet espace, leurs positions étant pour commencer instanciées par leur passage dans la première couche d'embeddings, puis par les hidden states successifs, dessinant ainsi leurs trajectoires dans l'espace d'activation.

A ce stade, une notion consubstantielle au principe des embeddings mais qui peut être un peu touchy à appréhender est celle de sémantique distribuée : dans cet espace vectoriel, **un concept n'est pas une entité discrète localisée en un point précis ou représenté par une dimension isolée, mais est encodé de manière distribuée le long de multiples directions vectorielles** (différents axes en quelque sorte), ce qui implique que certaines transformations syntaxiques ou sémantiques peuvent s’interpréter comme des déplacements au sein de cet espace, et ce qui explique pourquoi des concepts sémantiquement proches auront tendance à occuper des régions géométriquement voisines.

Ce qui est assez pratique par contre, c'est qu'en appliquant aux hidden states des techniques de réduction de dimension ([PCA ou UMAP](https://www.datacamp.com/fr/tutorial/understanding-dimensionality-reduction)), il est possible d'explorer empiriquement les changements de trajectoires qui s'opèrent au gré des couches. Le graphique ci-dessous représente par exemple l’évolution moyenne des représentations vectorielles des tokens du user prompt à travers les couches du modèle, obtenue en calculant pour chaque couche le complément de la similarité cosine entre les hidden state (agrégée sur l'ensemble des tokens) de la couche courante _ht_ et ceux de la couche précédente _hp_ soit _1−cos(ht,hp)_. La courbe obtenue montre ainsi la médiane des transformations (changements de direction) dans l'espace latent couche par couche, tandis que la zone ombrée correspond à l’intervalle interquartile (25–75 %). Pour résumer, on visualise de cette manière la tendance centrale de l'écart entre couches adjacentes et la variabilité entre tokens autour de cette tendance.

![Image 3: enter image description here](https://iaetbibliotheques.fr/content/images/hidden_states.png)

On observe ainsi une dynamique très cohérente avec le fonctionnement désormais connu des réseaux Transformer, à savoir, à partir des embeddings initiaux, une déviation relativement élevée dans les couches basses (les premières) où s'opèrent les plus fortes mises en forme lexicales et syntaxiques, suivie d'une diminution de ce mouvement dans les couches intermédiaires là où les représentations sémantiques se stabilisent, s'achevant avec une augmentation très marquée dans les toutes dernières couches hautes quand les représentations se réorganisent au plus proche de la sortie du modèle.

## Fine‑tuning et déformation de l’espace latent

Le fine-tuning, qui consiste à ajuster des poids du modèle en le ré-entraînant à partir de nouvelles données, agit directement sur les poids du modèle en modifiant les matrices de projection et les paramètres internes des couches. Par construction, il transforme la manière dont les hidden states évoluent d’une couche à l’autre, ce que l'on peut voir comme une déformation globale de la géométrie de l’espace latent dans lequel certaines directions deviennent plus saillantes ou sont au contraire atténuées.

C'est pourquoi le fine-tuning est très efficace (mais coûteux en données et calculs) pour modifier le comportement d'un modèle, et est irréversible quand il est appliqué sur tout le modèle (full fine-tuning).

## Le Steering

Le steering repose sur une approche différente, qui exploite le caractère distribué multi-dimensionnel du sens dans l’espace latent et se base sur l’hypothèse dite de la représentation linéaire ([Linear Representation Hypothesis](https://arxiv.org/abs/2311.03658)), selon laquelle les modèles de langage tendent à encoder certains attributs sémantiques ou stylistiques sous la forme de directions à peu près linéaires dans l’espace d’activation.

Ces deux principes de linéarité et de sémantique distribuée autorisent des **modifications locales des hidden states sans toucher aux poids du modèle, mais en effectuant de simples opérations arithmétiques sur les vecteurs** (ajout, soustraction, pondération) qui réorientent les trajectoires internes des tokens vers des directions sémantiques vectorielles déjà présentes afin de favoriser leur activation, ce qui suffit à biaiser l’output final.

Concrètement donc, pour un modèle donné, il s’agit d’identifier les valeurs d’un vecteur de direction associé à une idée, un attribut ou un comportement dans son espace latent, puis de l’injecter (éventuellement pondéré par un coefficient jouant le rôle de paramètre de force de "guidage") dans les hidden space d’une couche donnée par un hook lors de l’inférence, pour parvenir à modifier de manière ciblée le comportement, voire la personnalité, du modèle.

![Image 4: enter image description here](https://iaetbibliotheques.fr/content/images/dummy_steering.png)

_Ce graphique montre une projection fictive qui représentent les flèches des directions sémantiques distribuées, et où la trajectoire du token illustre son évolution à travers les couches. On y voit l'influence du steering comme biais directionnel qui pousse la trajectoire vers une région déjà présente de l’espace._

Traduit en code, le steering peut donc se résumer à l’ajout d’une ligne spécifique définissant ce hook, que l’on peut activer ou retirer à volonté.

**Exemple de code**
```
import torch

v = torch.load("steering_vectors.pt", map_location="cpu").to(device,dtype)

layer_idx = 15
coefficient = 4.0

def steering_hook(module, input, output):
    return output + coefficient * v

# Register
hook_handle = model.model.layers[layer_idx].mlp.register_forward_hook(steering_hook)
```

Une question demeure néanmoins : comment extraire un vecteur des représentations cachées d'un modèle ?

De fait il existe plusieurs techniques de détection de contexte qui, appliquées à partir de deux jeux de prompts contrastés spécifiquement construits, aident à à trouver la direction dans l'espace d'activation qui sépare au mieux ces deux familles de prompts, donc qui représente le mieux le concept cible :

*   Différence des moyennes (DiM) : on calcule la différence moyenne entre les exemples positifs et négatifs d'un concept
*   Régression logistique : on entraîne un classificateur linéaire pour faire la distinction entre les exemples positifs et négatifs, puis on récupère le vecteur de poids du classificateur comme direction de séparation.
*   Analyse en composantes principales (ACP) : on identifie la composante principale qui maximise la variances entre les deux ensembles.

Prenons un exemple concret avec comme objectif de forcer le modèle (notre LLama-3.2-3B-Instruct) à répondre avec un style académique et scientifique en utilisant la méthode de la différence des moyennes : on commence par constituer deux listes d’une centaine de prompts chacune (générés synthétiquement bien sûr :) !), tous sur la même thématique mais rédigés dans deux registres différents, soit de manière simple (“Explain what a black hole is.”) soit dans un style plus académique et technique (“Provide a concise scientific overview of black-hole formation and key properties in general relativity.”). Le but est alors de capturer dans l’espace d’activation du modèle la direction qui correspond à ce glissement de registre en (en résumé) :

*   passant les deux groupes de prompts au modèle ;
*   extrayant les vecteurs des hidden states au niveau d’une couche donnée ;
*   calculant les moyennes des deux groupes ;
*   prenant leur différence normalisée comme direction de steering

**Pseudo- code**
```
plain_prompts = [
    "Explain what a black hole is.",
    "Tell me how rainbows form.",
    "Why do we sleep?",
    "How do vaccines work?",
    "What causes earthquakes?",
    "Explain how a plane stays in the air.",
    "Why is the sky blue?",
    ...]
academic_prompts = [
    "Provide a concise scientific overview of black-hole formation and key properties in general relativity.",
    "Explain the optical physics underlying rainbow formation, including refraction, dispersion, and internal reflection.",
    "Summarise current neurobiological theories regarding the functions of human sleep and their empirical support.",
    "Describe the immunological mechanisms of vaccination, including antigen presentation, memory formation, and herd effects.",
    "Discuss the tectonophysical processes that generate earthquakes and the primary determinants of rupture magnitude.",
    "Analyse the aerodynamic principles enabling fixed-wing flight, emphasising lift generation and boundary-layer behaviour.",
    ...]
---
v_plain    = get_vector_chat(plain_prompts,    "Plain")
v_academic = get_vector_chat(academic_prompts, "Academic")
steering_vec = (v_academic - v_plain).to(device)
steering_vec = steering_vec / steering_vec.norm()
```

Une fois les coordonnées du vecteur représentant le champ lexical et stylistique académique récupérées, disons pour la couche intermédiaire 15, il suffit de le ré-injecter dans cette même couche pendant l'inférence avec un simple hook, ce qui donne par exemple pour le prompt "Explain what a black hole is." :

*   avant steering

```
A black hole is a region in space where the gravitational pull is so strong that nothing, including light, can escape. It is formed when a massive star collapses in on itself and its gravity becomes so strong that it warps the fabric of spacetime around it.

Here's a step-by-step explanation of how a black hole is formed:

 - **Massive star collapse**: A massive star runs out of fuel and dies. If the star is massive enough (about 3-4 times the size of the sun), its gravity collapses the star in on itself.
 - **Supernova explosion**: The star collapses, causing a massive explosion known as a supernova. This explosion expels a large amount of matter into space.
 - **Core collapse**: The core of the star collapses into a singularity, a point of infinite density and zero volume. This singularity is the center of the black hole.
 - **Event horizon**: The point of no return around a black hole is called the event horizon. Any matter or radiation that crosses the event horizon is trapped by the black hole's gravity and cannot escape.
```

*   après steering

```
**Introduction to Black Holes**

A black hole is a region of spacetime characterized by an extreme gravitational field, where the curvature of spacetime is so pronounced that the escape velocity exceeds the speed of light. This phenomenon is a consequence of the general theory of relativity, formulated by Albert Einstein.

**Formation and Properties**

Black holes are formed through the collapse of a massive, compact object, such as a stellar remnant (e.g., a neutron star or a supermassive compact object in a galactic center). The collapse of the object's mass-energy density leads to a singularity, a point of infinite density and zero volume, surrounded by an ergosphere, a region where the spacetime curvature is so intense that it warps the fabric of spacetime.

**Characteristics**

The following properties are inherent to black holes:

1. **Event Horizon**: The boundary of the black hole, marking the point of no return, where the gravitational field becomes so intense that any object exceeding the event horizon is inevitably drawn towards the singularity.
2. **Singularity**: The central point of the black hole, where the curvature of spacetime is infinite, and the laws of physics as we know them are inapplicable.
```

Voici pour cette implémentation basique, sachant qu'on peut d'une part complexifier le dispositif avec plusieurs opérations de plusieurs vecteurs à des couches différentes, et d'autre part profiter de cet méthode ultra-pédagogique pour entrer en profondeur dans le processus génératif des LLMs et explorer empiriquement beaucoup plus loin comment la connaissance est stockée dans leur géométrie interne via les impacts observés du steering en variant les couches et le coefficient de guidage (ce point fera l'objet d'un prochain billet).

## Conclusion

Le steering, en exploitant la manière dont le sens est déjà distribué dans l’espace latent, est une méthode implicite plus stable et robuste que le simple prompting explicite donné en input pour orienter le style ou le registre des réponses d’un modèle, et qui présente également l’avantage d’être finement **paramétrable, réversible, et activable ou désactivable à la volée lors de l’inférence**.

Cependant, si le steering est efficace pour contraindre le champ lexical, stylistique ou sémantique d’un modèle dans une direction donnée (style, sujet, persona, biais factuel), il est peu adapté à l’acquisition de nouvelles connaissances ou compétences et ne remplace pas le fine-tuning pour modifier durablement les capacités intrinsèques du modèle.

* * *

Source d'inspiration pour ce billet : cette [vidéo](https://www.youtube.com/watch?v=F2jd5WuT-zg) sur Youtube

* * *