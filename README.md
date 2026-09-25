# Persona — Newsletter IA personnalisée avec n8n

Projet Epitech Web Académie (W-AIA-201), réalisé en binôme.
Persona est un agrégateur d'actualités piloté par IA, sans interface graphique : tout passe par un chatbot.
Le chatbot inscrit l'utilisateur. Le workflow lui envoie ensuite par email une newsletter adaptée à ses centres d'intérêt.

Livrable : un workflow **n8n** exporté en JSON (`exports/app.json`).

## Ce qu'il fallait faire

D'après le sujet :

- un **déclencheur chatbot** unique qui gère tous les usages ;
- une **authentification** dans le workflow, pour qu'un utilisateur malveillant n'accède ni aux données des autres ni aux droits admin ;
- un utilisateur doit pouvoir demander une newsletter, donner ses centres d'intérêt, son email et la fréquence d'envoi voulue ;
- le workflow doit récupérer des actualités depuis **plusieurs sources**, choisir celles qui intéressent chaque utilisateur et lui envoyer par email des liens et des résumés, au moment qu'il a choisi ;
- respecter les bases du **RGPD** (stockage des emails, désabonnement) ;
- pour l'évaluation, présenter le projet en keynote comme une réponse à un appel d'offres, en justifiant le choix de n8n.

## Architecture du workflow

Le workflow contient deux flux.

### 1. Chatbot & authentification

`When chat message received` → recherche de la session dans MongoDB → `Switch` selon l'état de la session :

| État | Action |
|---|---|
| `new` | Crée la session et demande un nom d'utilisateur |
| `awaiting_username` | Cherche le nom : s'il existe, on passe en connexion, sinon en inscription |
| `awaiting_password_register` | Génère un sel aléatoire, hache le mot de passe et crée le compte (abonné par défaut) |
| `awaiting_password_login` | Hache le mot de passe saisi avec le sel puis le compare à la base. En cas d'erreur, on redemande |
| `authenticated` | Transmet le message à l'agent IA (Ollama, `granite3.1-moe:3b`) |

L'état de chaque conversation est enregistré dans la collection MongoDB `session`, indexée par `sessionId`.
Les réponses partent via des nœuds *Respond to Webhook*.

### 2. Newsletter planifiée (nœuds `NL …`)

```
Schedule / Manual
  → 3 flux RSS (The Guardian climat, Grist, Inside Climate News)
  → Merge → Normalize (titre, lien, extrait, date) → Dedupe (par lien)
  → Filtre < 7 jours → Limit 5 → Aggregate
  → Abonnés MongoDB (subscribed = true) → Filtre selon la fréquence / lastSentAt
  → LLM (llama-3.1-8b-instant) : choisit et résume les articles selon les intérêts
  → Code : parse le JSON du LLM et génère l'email HTML avec un lien de désabonnement
  → Envoi SMTP → mise à jour de lastSentAt
```

## RGPD & sécurité

- Seuls les utilisateurs `subscribed: true` reçoivent la newsletter, et chaque email contient un lien de désabonnement.
- Les mots de passe sont salés et hachés avant d'être stockés.
- Les données de session sont rattachées au `sessionId` du chat, donc un utilisateur ne peut pas lire la session d'un autre.
- Comme le demande le sujet, l'export JSON ne contient pas de secrets : seulement les noms et identifiants des credentials n8n.

## Lancer le projet

1. Lancer n8n en local (`npx n8n` ou Docker) et une instance MongoDB.
2. Importer `exports/app.json` dans n8n.
3. Créer les credentials : MongoDB, Ollama (avec `ollama pull granite3.1-moe:3b`), une API compatible OpenAI pour `llama-3.1-8b-instant` (par ex. Groq), et SMTP.
4. Changer l'adresse d'expéditeur du nœud `NL Send Email`.
5. Ouvrir le chat n8n pour s'inscrire, puis lancer `NL Manual` pour tester l'envoi.

## Limites connues

- Plusieurs expressions n8n n'ont pas les `{{ }}` (`=$json.hash`, `=$json.password`, la condition du nœud `If2`…). Elles sont donc lues comme du texte brut, et le hachage comme la vérification du mot de passe à la connexion ne marchent pas comme prévu.
- L'utilisateur ne peut pas encore donner ses centres d'intérêt ni sa fréquence depuis le chat. Le flux newsletter lit `interests` et `frequency` (1 jour par défaut) en base.
- Les sources RSS sont fixes et toutes sur le climat.
- Le lien de désabonnement pointe vers un formulaire n8n en `localhost`.
