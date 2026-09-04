---
title: "Stop Shipping Individual MCP Servers. Start Shipping Agent Plugins"
description: "L'abstraction utile suivante n'est pas une énième intégration d'outil, mais un paquet portable de capacités : MCP (accès aux outils/données) + Skills (savoir-faire procédural) empaquetés en Agent Plugins, standardisés le 6 août 2026 par la spec ouverte Agent Plugins 1.0 (Vercel + Amazon, Anysphere/Cursor, Microsoft, OpenAI, Google)."
url: https://medium.com/data-science-collective/stop-shipping-individual-mcp-servers-start-shipping-agent-plugins-8174d2a248b0
author: Andrii Tkachuk (Data Science Collective)
date: 2026-08-30
---

# Stop Shipping Individual MCP Servers. Start Shipping Agent Plugins

*The next useful abstraction isn't another tool integration. It's a portable package of capabilities.*

La façon d'équiper les agents a suivi une séquence logique : d'abord connecter les modèles aux vrais systèmes, puis leur apprendre à s'en servir correctement, et maintenant empaqueter les deux ensemble. En août 2026, cette idée d'empaquetage cesse d'être un pattern propriétaire pour devenir une spécification ouverte. L'intérêt de l'auteur n'est pas de dire qu'il faut tout reconstruire autour des plugins, mais que **les plugins deviennent une couche de distribution pour les capacités d'agent** : au lieu de livrer un serveur MCP, une Skill, des instructions et un README d'assemblage, on empaquette ce savoir et ces intégrations en une unité réutilisable, désormais dotée d'une définition ouverte et neutre.

## MCP answered: "What can the agent connect to?"

Le Model Context Protocol (Anthropic, nov. 2024) répond à la fragmentation : une interface standard entre un client IA et des outils/données externes. Un système exposé une fois via MCP est utilisable par tout client compatible. Mais la connectivité n'est qu'une partie du problème : donner un outil `search_documents` ne dit pas à l'agent *quand* l'utiliser, *comment* le combiner, quelle séquence est sûre, ce que l'organisation considère comme un workflow correct. MCP dit ce que l'agent *peut* appeler, pas *comment* le travail doit être fait.

## Skills answered: "How should the agent perform the work?"

Les Agent Skills (Anthropic, 2025 ; format publié ensuite comme standard ouvert portable) comblent ce vide : des dossiers d'instructions, scripts et ressources que les agents découvrent et chargent au besoin. Une Skill encode *comment* faire une revue d'architecture, investiguer un incident, quels contrôles avant un déploiement, quelles conventions internes suivre. Les outils restent génériques ; la Skill décrit le workflow (chercher la connaissance interne d'abord, vérifier dans Salesforce, signaler les conflits, citer chaque affirmation, ne jamais écrire pendant la recherche, produire le rapport selon la structure approuvée).

Exemple d'arborescence :
```
skills/
└── architecture-review/
    ├── SKILL.md
    ├── references/
    │   └── architecture-checklist.md
    └── scripts/
        └── validate_diagram.py
```

## Then the next problem becomes obvious

Faire réutiliser une capacité par un collègue = lui remettre un serveur MCP, un autre serveur MCP, trois Skills, deux fichiers de config, des variables d'environnement, des instructions d'agent, peut-être un hook, peut-être un sous-agent, et un README de vingt étapes. Techniquement réutilisable, opérationnellement pénible. C'est le problème que les plugins commencent à adresser.

## What is an Agent Plugin?

Un plugin est un **paquet de capacités d'agent**. Les vendeurs diffèrent : Anthropic décrit les plugins Claude comme bundlant Skills, connecteurs et sous-agents (Claude Code ajoutant hooks + config MCP) ; OpenAI utilise les plugins dans ChatGPT et Codex comme workflows empaquetés.

Le développement important : le **6 août 2026**, la **spécification Agent Plugins 1.0** est rendue publique — format de paquet ouvert et neutre, initié par **Vercel** et développé avec **Amazon, Anysphere (Cursor), Microsoft, OpenAI**, **Google** rejoignant peu après comme mainteneur principal. Le cœur portable v1 ne contient volontairement que **deux types de composants** — Agent Skills et serveurs MCP :
```
customer-research/
├── plugin.json
├── skills/
│   └── customer-research/
│       ├── SKILL.md
│       └── references/
│           └── report-template.md
└── mcp.json
```
Le manifeste identifie le paquet, `skills/` porte le savoir procédural, `mcp.json` décrit les serveurs MCP nécessaires. Tout client compatible découvre les deux depuis le même dossier. GitHub a livré le support dans VS Code, Copilot CLI et l'app Copilot le 12 août ; fin août, ChatGPT, Codex, Cursor et Kiro figuraient parmi les clients de lancement.

## Why this is more interesting than "another plugin system"

Ce qui compte, c'est *ce qui* est empaqueté. Une capacité d'agent a deux dimensions : la capacité d'agir et le savoir *comment* agir — MCP + Skill. Un plugin empaquette les deux en une capacité réutilisable : au lieu de distribuer deux serveurs MCP, deux Skills et une pile d'instructions, on distribue `customer-research-plugin`, qui représente **un vrai travail à faire, pas juste une intégration**. Pattern lié : un agent ne devrait pas raisonner en commandes bas-niveau (`salesforce.search_accounts`, `postgres.query`) mais en **opérations/capacités stables** (`research_customer`, `prepare_rfi`, `investigate_incident`), chacune pouvant nécessiter en interne une Skill pour la procédure et plusieurs MCP pour les données.

## A practical example

Besoin : donner à des consultants un agent qui recherche un client via la connaissance interne et Salesforce, puis génère un rapport structuré. La réponse traditionnelle enfle (UI, auth, historique, déploiement, dépôt, service). Mais si l'entreprise a déjà un environnement d'agent (Claude, Codex, Copilot), on empaquette un plugin dont la Skill sait quelles sources vérifier, dans quel ordre, quelles preuves exiger, quelles actions sont en lecture seule, comment structurer le rapport, les MCP fournissant l'accès. Installer, demander « recherche Acme Corp et prépare notre rapport standard » — première version utilisable, sans UI de chat ni service d'orchestration. Un serveur MCP brut est souvent trop bas-niveau pour être l'unité finale de distribution : il donne la connectivité, pas le modèle opérationnel autour. Même MCP, savoir opérationnel différent selon le workflow → empaqueter les Skills avec le MCP rend le MCP infrastructure et le plugin la capacité distribuable.

## How you'd actually publish and install one

On pousse le dossier plugin dans un dépôt Git et on ajoute un petit manifeste de marketplace à côté — `.claude-plugin/marketplace.json` (Claude Code), `.github/plugin/marketplace.json` (GitHub Copilot). Ce fichier liste nom, version et emplacement du `plugin.json`. Push, tag une release, et **le dépôt lui-même EST la marketplace** ; pas d'hébergement séparé. Côté réception : le collègue pointe son client vers le dépôt une fois (`claude plugin marketplace add your-org/your-repo`) puis installe par nom (`claude plugin install customer-research@your-org`). Certains clients permettent d'installer directement depuis une URL Git. Le client tire lui-même les fichiers Skill et la config MCP — plus de copie manuelle de SKILL.md ni de câblage MCP à la main.

## Plugins are not magically portable

« Plugin » ne signifie pas qu'un paquet Claude s'installe à l'identique partout. Les systèmes vendeurs diffèrent (hooks, agents custom, surfaces UI, modèles de permission, commandes, distribution). Le format Claude n'était pas parmi les clients de lancement de la spec 1.0 en août — il garde son propre layout. D'où l'intérêt de la spec : définir un **plancher de portabilité** (Agent Skills + config MCP) et laisser le reste au client, via un **namespace d'extension** pour le proprio sans polluer le cœur portable :
```
my-plugin/
├── plugin.json          # portable
├── skills/              # portable
├── mcp.json             # portable
└── com.vendor.client/   # vendor-specific extension
    └── hooks/
```

## The model I'd use in production

Garder le cœur (Skill + MCP) aussi portable que possible, traiter le paquet plugin comme couche de distribution par-dessus, et laisser les extensions vendeur au bord. Ainsi une équipe Claude Code et une équipe Codex ne recréent pas la capacité de zéro : même cœur, on adapte seulement ce que le client cible exige. Définir une capacité interne une fois (`architecture-review` avec ses checklists et son `mcp.json` pointant vers le dépôt d'architecture, la base de connaissances, les findings sécurité) et laisser un utilisateur Copilot, Claude et Codex atteindre le même savoir procédural et les mêmes systèmes, seul l'adaptateur runtime changeant.

## Discovery is not authorization

Principe de sécurité explicite : **discoverable, installed, authorized, enabled, executable** sont cinq états distincts ; en satisfaire un ne dit rien des autres. Un plugin peut être installé et l'utilisateur autorisé sur le système sous-jacent, tandis que la politique d'agent garde une opération désactivée (un agent de recherche client tournant sur une connexion Salesforce qui permet techniquement de supprimer une opportunité, sans jamais y être autorisé). Les plugins simplifient la distribution ; ils ne doivent pas effondrer les frontières de gouvernance. Un plugin mérite la même vigilance qu'une dépendance : qui le possède, quels MCP il connecte, s'il exécute du code local, quelles permissions, quelles Skills influencent le comportement, s'il peut écrire. (GitHub lie la gouvernance aux réglages entreprise + allowlists MCP ; Anthropic avertit que les plugins peuvent contenir des MCP locaux tournant avec les permissions de la machine ; OpenAI sépare installation et autorisation des apps connectées.)

## Where this leaves MCP and Skills

MCP devient moins une frontière produit et plus une **infrastructure** — un plugin porte les instructions qui rendent une intégration MCP immédiatement utilisable. Au lieu d'installer un serveur Jira MCP brut, on installe un plugin `incident-response` qui sait déjà investiguer un incident, préparer un postmortem, créer les tickets de suivi, câblé à Jira, Datadog et les runbooks internes. L'utilisateur n'achète pas une intégration : il acquiert une **capacité opérationnelle**. Les Skills restent aussi importantes : « MCP dit *voici un outil de déploiement*. Une Skill dit *voici comment notre entreprise déploie sans risque*. Un plugin dit *voici la capacité de déploiement complète*. »

## What I would not do

Ne pas réécrire une plateforme mûre juste parce que les Agent Plugins existent ; ne pas forcer chaque workflow en plugin ; ne pas mettre de logique métier critique dans un manifeste propriétaire ; ne pas croire qu'installer un paquet règle identité, politique, observabilité ou gouvernance. Certains workflows exigent un état durable, du traitement d'événements complexe, des garanties transactionnelles, une UX riche ou une isolation runtime stricte. La question utile reste étroite : est-ce vraiment une nouvelle application, ou une capacité qui peut tourner dans un environnement d'agent déjà présent ? Si c'est le second cas, l'empaqueter en plugin vaut la considération.

## The bigger shift: from tool marketplace to capability marketplace

Les marketplaces d'intégration s'organisent autour de produits (Slack, Salesforce, Jira MCP…). Une marketplace agent-native peut s'organiser autour d'**outcomes** : investiguer un incident, préparer une recherche client, réviser une architecture, planifier une release — chacun dépendant de plusieurs systèmes en interne. Plus proche de la délégation humaine (« investigate the incident », pas « use Jira, Datadog, and GitHub »). Signal : les utilisateurs ne se soucient pas de savoir si c'est un MCP, une Skill, un plugin ou un workflow — ils se soucient de *ce que l'agent peut faire*. Cela pointe vers un **registre de capacités** (décrivant la capacité, son propriétaire, son niveau de risque, son statut d'approbation) plutôt que des catalogues séparés par primitive, la plateforme décidant comment la matérialiser pour Claude, Codex ou un agent interne, la capacité restant stable pendant que l'implémentation évolue.

## A useful timeline

MCP (2024) a standardisé l'accès aux outils et données. Agent Skills (2025) a empaqueté le savoir procédural. Agent Plugins (2026) empaquette des capacités réutilisables pour la distribution. Pas de remplacement : les couches se composent — un plugin peut contenir des Skills et référencer des MCP, et ces Skills apprennent à l'agent comment utiliser les outils MCP sous-jacents. **Composition progressive**, pas remplacement.

## Final take

On a d'abord standardisé comment les agents se connectent aux systèmes, puis comment leur donner un savoir procédural, maintenant comment ces capacités sont empaquetées et distribuées. « The important part isn't the word *plugin* — it's that agent capabilities are becoming composable, versionable, distributable, and increasingly portable. »

## References

- Anthropic, Introducing the Model Context Protocol (Nov 25, 2024)
- Anthropic, Equipping agents for the real world with Agent Skills (Oct 16, 2025; portability update Dec 18, 2025)
- Agent Plugins, Agent Plugins Specification 1.0.0 — https://github.com/agentplugins/agent-plugins-spec
- Vercel, Introducing Agent Plugins (Aug 6, 2026)
- Google Developers Blog, Agent Plugins package your skills, tools, and more
- AWS Open Source Blog, AWS Supports Agent Plugins: An Open Standard for Portable Agent Extensions
- GitHub, Agent Plugins 1.0 in VS Code, Copilot CLI, and the Copilot app (Aug 12, 2026)
- OpenAI, Plugins in ChatGPT and Codex
