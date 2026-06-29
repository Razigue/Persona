# PERSONA — On a silver platter (Epitech)

## Livrable
- deliverable name: `*.json`
- language: JSON
- compilation: none
- Authorized tools: N8N

## Contexte projet
Persona est un agrégateur d'actualités basé sur l'IA, personnalisable, sans interface graphique.
Interaction via un chatbot qui demande/déduit : identifiants, adresse email, centres d'intérêt.
Le chatbot construit une newsletter personnalisée envoyée selon une fréquence adaptable.

Ce projet n'existe pas encore. Objectif : utiliser N8N pour construire les workflows qui donnent vie à ce projet.

## N8N
- Outil open-source d'automatisation de workflows, no-code mais permet d'intégrer du code.
- Nœuds officiels nombreux + communauté active.
- **Nœuds communautaires autorisés** pour ce projet.
- Gratuit en hébergement local (solution recommandée pour ce projet).
- Accès à des outils d'IA connectables à différents modèles (locaux ou en ligne), libre choix.
- Note : tokens gratuits disponibles pour étudiants avec certains modèles.
- **La qualité du modèle utilisé n'est pas un critère d'évaluation.**

## Points importants — Interface
- Le workflow doit comporter un **nœud de déclenchement Chatbot** utilisable pour tous les usages.
- Mécanisme requis empêchant les acteurs malveillants d'accéder : aux données personnelles, aux fichiers de configuration des autres utilisateurs, aux privilèges administrateur.
- Implique un **processus d'authentification utilisateur** à l'intérieur du workflow, testé de manière approfondie.
- Toute fonctionnalité future doit être compatible avec ce système d'authentification.
- D'autres nœuds de déclenchement peuvent être ajoutés (plus pratiques pour certaines tâches), mais **le workflow doit fonctionner sans eux**.

## Fonctionnalités obligatoires

Un utilisateur doit pouvoir :
- Demander une newsletter via le déclencheur chatbot
- Donner ses centres d'intérêt
- Fournir son adresse e-mail
- Demander une fréquence ou un moment idéal pour recevoir la newsletter

Le workflow doit :
- Récupérer des actualités à partir de **plusieurs sources**
- Décider quelles actualités sont pertinentes pour quel utilisateur
- Envoyer une newsletter contenant des liens et des résumés pour chaque actualité, au moment choisi par l'utilisateur, à son adresse e-mail
- Tenter de respecter les **principes de base du RGPD** (question posée par le sujet : êtes-vous autorisés à stocker les adresses email ? comment un utilisateur peut-il se désabonner ?)
- Se protéger contre les **utilisateurs malveillants**

## Fonctionnalités supplémentaires (à faire une fois le workflow de base fonctionnel)

Chaque item porte un "indicateur de valeur" (non détaillé numériquement dans le texte source) servant à prioriser.

Un utilisateur **pourrait** pouvoir :
- Définir/choisir/se voir attribuer un prompt personnalisé (plus/moins d'actualités, format différent, ton différent…)
- Payer pour un produit amélioré (ou après période d'essai gratuite)
- Commenter une newsletter déjà reçue et demander des modifications pour la suivante (ex: ne plus utiliser telle source)
- Référencer un autre utilisateur via son pseudo et consulter les informations publiques de cet utilisateur

Un autre type d'utilisateur **pourrait** pouvoir :
- Demander que ses publicités soient affichées dans les newsletters d'un sous-ensemble d'utilisateurs (ex: personnes intéressées par la science) — pose des défis RGPD et UX supplémentaires (mentionné explicitement par le sujet, sans solution donnée)

Le workflow **pourrait** :
- Vérifier les faits des actualités en les confrontant à d'autres sources/moyens
- Rédiger des newsletters en HTML plutôt qu'en texte brut
- Proposer d'autres moyens de réception adaptés au format (article de blog, post réseau social, version imprimable…)
- Utiliser l'IA générative ou stocker des sources pour des newsletters multimédias
- Être utilisable via de véritables endpoints exposés (site web, canal Discord, e-mail…)

Vous **pourriez** :
- Fournir un audit de sécurité du workflow
- Fournir la preuve d'une conformité RGPD complète
- Optimiser certaines parties pour réduire temps d'exécution et utilisation de tokens
- Utiliser RAG et serveurs MCP pour augmenter les capacités de l'agent IA

## Keynote (évaluation)
- Le projet est évalué via une **présentation Keynote** démontrant les fonctionnalités.
- La keynote répond à un (faux) appel d'offres ; les outils autorisés n'étaient pas précisés par le client fictif → il faut **justifier le choix de N8N** fait par le (faux) responsable.

### E-mail du (faux) product manager — points de focalisation
Citation du sujet :
> "À partir du brief client, je vois toujours les mêmes points revenir : un déclencheur chatbot unique, une gestion sécurisée des identifiants utilisateurs et des données personnelles, la récupération et le filtrage des actualités en temps réel, ainsi qu'une newsletter claire envoyée par e-mail selon une planification. Ce sont les éléments principaux qu'ils souhaitent voir parfaitement maîtrisés en premier. Gardons donc notre prototype concentré sur ces points. De plus, ils sont basés aux États-Unis mais pourraient vouloir utiliser leur produit en Europe à l'avenir, donc nous devrions commencer à travailler un peu sur cette question du RGPD."

### Avertissement sécurité (anonymisation avant partage)
- Les fichiers JSON exportés d'un workflow N8N incluent les **noms et identifiants des credentials**.
- Les identifiants eux-mêmes ne sont pas sensibles, mais **les noms peuvent l'être** selon la nomenclature choisie.
- Les nœuds **HTTP Request** peuvent contenir des **en-têtes d'authentification** s'ils sont importés depuis cURL.
- Consigne explicite : **supprimer ou anonymiser ces informations dans le JSON avant de le partager**.

## Version du document
v 1.2
