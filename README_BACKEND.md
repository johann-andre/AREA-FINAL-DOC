# Documentation Complète - AREA Backend API

## Table des Matières

1. [Vue d'ensemble](#vue-densemble)
2. [Architecture du Projet](#architecture-du-projet)
3. [Installation et Configuration](#installation-et-configuration)
4. [Modèles de Données](#modèles-de-données)
5. [API Endpoints](#api-endpoints)
6. [Authentification et OAuth](#authentification-et-oauth)
7. [Services Intégrés](#services-intégrés)
8. [Système de Workflows (AREA)](#système-de-workflows-area)
9. [Gestion des Tokens](#gestion-des-tokens)
10. [Sécurité](#sécurité)
11. [Déploiement](#déploiement)
12. [Troubleshooting](#troubleshooting)

---

## 1. Vue d'ensemble

### Description
AREA (Action-REAction) est une plateforme d'automatisation permettant de connecter différents services via un système de workflows. Lorsqu'une action se produit sur un service, une réaction automatique est déclenchée sur un autre service.

### Stack Technique
- **Backend**: Node.js + Express.js
- **ORM**: Sequelize
- **Base de données**: MySQL/PostgreSQL (via Sequelize)
- **Authentification**: JWT + OAuth 2.0
- **Containerisation**: Docker

### Services Supportés
- GitHub
- Google (Drive, Calendar, YouTube, Gmail)
- LinkedIn
- Discord
- Microsoft (Outlook, Teams)
- Twitter/X
- Twitch
- Notion
- Trello

---

## 2. Architecture du Projet

### Structure des Dossiers

```
AREA-Backend/
├── Api/
│   ├── Controllers/
│   │   ├── Authentification/
│   │   │   ├── login.js
│   │   │   ├── register.js
│   │   │   ├── Oauth.js
│   │   │   └── refreshToken.js
│   │   ├── Action.js
│   │   ├── Reaction.js
│   │   ├── Service.js
│   │   ├── Area.js
│   │   ├── Admin.js
│   │   ├── user.controler.js
│   │   └── TemplateArea.js
│   └── Routes/
│       ├── Authentification/
│       │   ├── login.js
│       │   ├── register.js
│       │   ├── Oauth.js
│       │   └── refreshToken.js
│       ├── Actions.js
│       ├── Reactions.js
│       ├── Service.js
│       ├── Area.js
│       ├── Admin.js
│       ├── User.js
│       ├── TemplateArea.js
│       └── about.js
├── Database/
│   ├── config/
│   │   └── db.js
│   └── models/
│       ├── User.js
│       ├── Services.js
│       ├── Actions.js
│       ├── Reactions.js
│       ├── Workflow.js
│       ├── Admin.js
│       └── Template_area.js
├── Module/
│   ├── ServicesManagement/
│   │   ├── polling.js
│   │   ├── Calendar/
│   │   ├── Discord/
│   │   ├── Github/
│   │   ├── Gmail/
│   │   ├── Google_Calendar/
│   │   ├── hook/
│   │   ├── Linkedin/
│   │   ├── Microsoft_Calendar/
│   │   ├── Notion/
│   │   └── Outlook/
│   └── TokenScheduler.js
├── server.js
├── Dockerfile
├── package.json
└── .env
```

### Architecture Logique

```
Client (Web/Mobile)
        ↓
  Express Server
        ↓
    ┌───────────────────────┐
    │   Authentification    │
    │   (JWT + OAuth 2.0)   │
    └───────────────────────┘
            ↓
    ┌───────────────────────┐
    │    API Routes         │
    └───────────────────────┘
            ↓
    ┌───────────────────────┐
    │    Controllers        │
    └───────────────────────┘
            ↓
    ┌───────────────────────┐
    │    Models (ORM)       │
    └───────────────────────┘
            ↓
    ┌───────────────────────┐
    │    Database (SQL)     │
    └───────────────────────┘
            ↓
    ┌───────────────────────┐
    │   Services Externes   │
    │  (GitHub, Google...)  │
    └───────────────────────┘
```

---

## 3. Installation et Configuration

### Prérequis
- Node.js >= 18.x
- npm ou yarn
- MySQL ou PostgreSQL
- Docker (optionnel)

### Installation

```bash
# Cloner le projet
git clone <repository-url>
cd AREA-Backend

# Installer les dépendances
npm install
```

### Configuration (.env)

```env
# Server
PORT=8080
NODE_ENV=development

# Database
DB_HOST=localhost
DB_PORT=3306
DB_NAME=area_db
DB_USER=root
DB_PASSWORD=your_password
DB_DIALECT=mysql

# JWT
LOGIN_SECRET_KEY=your_super_secret_jwt_key_min_256_bits

# OAuth URLs
BASE_URL=http://localhost:8080
FRONTEND_URL=http://localhost:8081

# GitHub OAuth
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret

# Google OAuth
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret

# LinkedIn OAuth
LINKEDIN_CLIENT_ID=your_linkedin_client_id
LINKEDIN_CLIENT_SECRET=your_linkedin_client_secret

# Discord OAuth
DISCORD_CLIENT_ID=your_discord_client_id
DISCORD_CLIENT_SECRET=your_discord_client_secret

# Microsoft OAuth
MICROSOFT_CLIENT_ID=your_microsoft_client_id
MICROSOFT_CLIENT_SECRET=your_microsoft_client_secret

# Twitter OAuth
TWITTER_CLIENT_ID=your_twitter_client_id
TWITTER_CLIENT_SECRET=your_twitter_client_secret

# Twitch OAuth
TWITCH_CLIENT_ID=your_twitch_client_id
TWITCH_CLIENT_SECRET=your_twitch_client_secret

# Notion OAuth
NOTION_CLIENT_ID=your_notion_client_id
NOTION_CLIENT_SECRET=your_notion_client_secret

# Trello OAuth
TRELLO_CLIENT_ID=your_trello_client_id
TRELLO_CLIENT_SECRET=your_trello_client_secret
```

### Démarrage

```bash
# Mode développement
npm start

# Avec Docker
docker build -t area-backend .
docker run -p 8080:8080 --env-file .env area-backend
```

---

## 4. Modèles de Données

### User (Utilisateur)

```javascript
{
  id: INTEGER (PK, Auto-increment),
  name: STRING,
  email: STRING (UNIQUE, NOT NULL),
  password: STRING (Hashed),
  info: JSON {
    github: {
      provider: "github",
      providerId: STRING,
      accessToken: STRING,
      refreshToken: STRING,
      email: STRING,
      isConnected: BOOLEAN
    },
    google: { ... },
    linkedin: { ... },
    discord: { ... },
    microsoft: { ... },
    twitter: { ... },
    twitch: { ... },
    notion: { ... },
    trello: { ... },
    timer: {
      provider: "timer",
      isConnected: BOOLEAN,
      activatedAt: DATETIME
    }
  },
  createdAt: DATETIME,
  updatedAt: DATETIME
}
```

### Service

```javascript
{
  id: INTEGER (PK, Auto-increment),
  name: STRING (UNIQUE, NOT NULL),
  description: TEXT,
  icon: STRING (URL),
  color: STRING (HEX),
  createdAt: DATETIME,
  updatedAt: DATETIME
}
```

### Action

```javascript
{
  id: INTEGER (PK, Auto-increment),
  service_id: INTEGER (FK -> Services),
  name: STRING (UNIQUE, NOT NULL),
  description: TEXT,
  parameters: JSON,
  createdAt: DATETIME,
  updatedAt: DATETIME
}
```

### Reaction

```javascript
{
  id: INTEGER (PK, Auto-increment),
  service_id: INTEGER (FK -> Services),
  name: STRING (UNIQUE, NOT NULL),
  description: TEXT,
  parameters: JSON,
  createdAt: DATETIME,
  updatedAt: DATETIME
}
```

### Workflow (Area)

```javascript
{
  id: INTEGER (PK, Auto-increment),
  user_id: INTEGER (FK -> User),
  name: STRING (NOT NULL),
  description: TEXT,
  action_id: INTEGER (FK -> Actions),
  reaction_id: INTEGER (FK -> Reactions),
  Params: JSON {
    action: { /* paramètres spécifiques */ },
    reaction: { /* paramètres spécifiques */ }
  },
  services: JSON (ARRAY),
  last_triggered_value: TEXT,
  active: BOOLEAN (DEFAULT: true),
  createdAt: DATETIME,
  updatedAt: DATETIME
}
```

### AreaTemplate (Template de Workflow)

```javascript
{
  id: INTEGER (PK, Auto-increment),
  name: STRING (UNIQUE, NOT NULL),
  description: TEXT,
  action_id: INTEGER (FK -> Actions),
  reaction_id: INTEGER (FK -> Reactions),
  Params: JSON,
  services: JSON (ARRAY),
  category: STRING,
  popularity: INTEGER,
  createdAt: DATETIME,
  updatedAt: DATETIME
}
```

### Admin

```javascript
{
  id: INTEGER (PK, Auto-increment),
  name: STRING,
  email: STRING (UNIQUE),
  role: STRING,
  permissions: JSON,
  createdAt: DATETIME,
  updatedAt: DATETIME
}
```

---

## 5. API Endpoints

### 5.1 Authentification

#### POST /api/register
Créer un nouveau compte utilisateur.

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Response (201):**
```json
{
  "message": "User created",
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

**Errors:**
- `400`: Email ou mot de passe manquant
- `400`: Utilisateur existe déjà
- `500`: Erreur serveur

---

#### POST /api/login
Connexion avec email et mot de passe.

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Response (200):**
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "userID": 1
}
```

**Errors:**
- `404`: Utilisateur non trouvé
- `401`: Mot de passe incorrect
- `500`: Erreur serveur

---

#### POST /api/refresh
Rafraîchir un access token OAuth expiré.

**Request Body:**
```json
{
  "userId": 1,
  "provider": "google"
}
```

**Response (200):**
```json
{
  "message": "Token refreshed successfully",
  "accessToken": "new_access_token",
  "refreshToken": "new_refresh_token"
}
```

**Providers supportés:**
- `google`
- `github`
- `discord`
- `linkedin`
- `microsoft`
- `twitter`

---

### 5.2 OAuth 2.0

#### GET /api/auth/{provider}
Initier le flux OAuth pour un provider.

**Paramètres Query:**
- `userId` (optionnel): ID de l'utilisateur
- `plateforme` (optionnel): `web` ou `mobile`

**Providers disponibles:**
- `/api/auth/github`
- `/api/auth/google`
- `/api/auth/linkedin`
- `/api/auth/discord`
- `/api/auth/microsoft`
- `/api/auth/twitter`
- `/api/auth/twitch`
- `/api/auth/notion`
- `/api/auth/trello`

**Exemple:**
```
GET /api/auth/google?userId=1&plateforme=web
```

**Réponse:**
Redirection vers la page d'autorisation du provider.

---

#### GET /api/auth/{provider}/callback
Callback OAuth (géré automatiquement par le provider).

**Traitement:**
1. Récupération du code d'autorisation
2. Échange contre un access token
3. Récupération des informations utilisateur
4. Stockage dans la base de données
5. Redirection vers le frontend

---

### 5.3 Services

#### GET /api/service
Récupérer tous les services.

**Response (200):**
```json
[
  {
    "id": 1,
    "name": "GitHub",
    "description": "Version control platform",
    "icon": "https://...",
    "color": "#181717"
  },
  ...
]
```

---

#### GET /api/service/:id
Récupérer un service par ID.

**Response (201):**
```json
{
  "id": 1,
  "name": "GitHub",
  "description": "Version control platform"
}
```

---

#### POST /api/service
Créer un nouveau service.

**Request Body:**
```json
{
  "name": "NewService",
  "description": "Service description",
  "icon": "https://icon-url.com",
  "color": "#FF5733"
}
```

**Response (201):**
```json
{
  "id": 10,
  "name": "NewService",
  ...
}
```

---

#### PUT /api/service/:id
Mettre à jour un service.

**Request Body:**
```json
{
  "description": "Updated description"
}
```

**Response (200):**
```json
{
  "message": "Service updated successfully",
  "service": { ... }
}
```

---

#### DELETE /api/service/:id
Supprimer un service.

**Response (200):**
```json
{
  "message": "Service supprimé avec succès"
}
```

---

### 5.4 Actions

#### GET /api/action
Récupérer toutes les actions.

**Response (200):**
```json
[
  {
    "id": 1,
    "service_id": 1,
    "name": "new_issue",
    "description": "Triggered when a new issue is created",
    "parameters": { ... }
  },
  ...
]
```

---

#### GET /api/action/id/:id
Récupérer une action par ID.

---

#### GET /api/action/name/:name
Récupérer une action par nom.

**Exemple:**
```
GET /api/action/name/new_issue
```

**Response (200):**
```json
{
  "id": 1,
  "name": "new_issue",
  "description": "Triggered when a new issue is created"
}
```

---

#### POST /api/action
Créer une nouvelle action.

**Request Body:**
```json
{
  "service_id": 1,
  "name": "new_pull_request",
  "description": "Triggered when a new PR is created",
  "parameters": {
    "repo": "string",
    "branch": "string"
  }
}
```

---

#### PUT /api/action/:id
Mettre à jour une action.

---

#### DELETE /api/action/:id
Supprimer une action.

---

### 5.5 Reactions

#### GET /api/reaction
Récupérer toutes les réactions.

---

#### GET /api/reaction/:id
Récupérer une réaction par ID.

---

#### GET /api/reaction/name/:name
Récupérer une réaction par nom.

---

#### POST /api/reaction
Créer une nouvelle réaction.

**Request Body:**
```json
{
  "service_id": 2,
  "name": "send_email",
  "description": "Send an email via Gmail",
  "parameters": {
    "to": "string",
    "subject": "string",
    "body": "string"
  }
}
```

---

#### PUT /api/reaction/:id
Mettre à jour une réaction.

---

#### DELETE /api/reaction/:id
Supprimer une réaction.

---

### 5.6 Workflows (Area)

#### GET /api/area
Récupérer tous les workflows.

**Response (200):**
```json
[
  {
    "id": 1,
    "user_id": 1,
    "name": "GitHub to Gmail",
    "description": "Send email when new issue",
    "action_id": 1,
    "reaction_id": 5,
    "Params": { ... },
    "active": true
  },
  ...
]
```

---

#### GET /api/area/:id
Récupérer un workflow par ID.

---

#### GET /api/area/user/:id
Récupérer tous les workflows d'un utilisateur.

**Exemple:**
```
GET /api/area/user/1
```

**Response (200):**
```json
[
  {
    "id": 1,
    "user_id": 1,
    "name": "My Workflow",
    ...
  }
]
```

---

#### GET /api/area/service/:service
Récupérer les workflows utilisant un service spécifique.

**Exemple:**
```
GET /api/area/service/github
```

---

#### POST /api/area
Créer un nouveau workflow.

**Request Body:**
```json
{
  "user_id": 1,
  "name": "GitHub to Discord",
  "description": "Notify Discord on new GitHub issue",
  "action_id": 1,
  "reaction_id": 10,
  "Params": {
    "action": {
      "repo": "myuser/myrepo"
    },
    "reaction": {
      "webhook_url": "https://discord.com/api/webhooks/...",
      "message": "New issue: {{title}}"
    }
  },
  "services": ["github", "discord"],
  "active": true
}
```

**Response (201):**
```json
{
  "id": 15,
  "user_id": 1,
  "name": "GitHub to Discord",
  ...
}
```

---

#### PUT /api/area/:id
Mettre à jour un workflow.

**Request Body:**
```json
{
  "active": false
}
```

---

#### DELETE /api/area/:id
Supprimer un workflow.

---

### 5.7 Templates

#### GET /api/templates
Récupérer tous les templates de workflows.

**Response (200):**
```json
[
  {
    "id": 1,
    "name": "GitHub Issues to Email",
    "description": "Get notified by email for new GitHub issues",
    "action_id": 1,
    "reaction_id": 5,
    "category": "Productivity",
    "popularity": 150
  },
  ...
]
```

---

#### GET /api/templates/:id
Récupérer un template par ID.

---

#### POST /api/templates/create-workflow
Créer un workflow à partir d'un template.

**Request Body:**
```json
{
  "templateId": 1,
  "userId": 1,
  "customParams": {
    "action": {
      "repo": "myuser/myrepo"
    },
    "reaction": {
      "email": "user@example.com"
    }
  }
}
```

**Response (201):**
```json
{
  "message": "Workflow created successfully from template",
  "workflow": { ... }
}
```

---

#### POST /api/templates
Créer un nouveau template.

---

#### PUT /api/templates/:id
Mettre à jour un template.

---

#### DELETE /api/templates/:id
Supprimer un template.

---

### 5.8 Users

#### GET /api/user
Récupérer tous les utilisateurs.

---

#### GET /api/user/:id
Récupérer un utilisateur par ID.

**Response (201):**
```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com",
  "info": {
    "github": {
      "isConnected": true,
      "email": "john@github.com"
    },
    "google": {
      "isConnected": true
    }
  }
}
```

---

#### PUT /api/user/:id
Mettre à jour un utilisateur.

---

#### DELETE /api/user/:id
Supprimer un utilisateur.

---

### 5.9 Admin

#### GET /api/admin
Récupérer tous les administrateurs.

---

#### GET /api/admin/:id
Récupérer un admin par ID.

---

#### POST /api/admin
Créer un nouvel administrateur.

---

#### PUT /api/admin/:id
Mettre à jour un administrateur.

---

#### DELETE /api/admin/:id
Supprimer un administrateur.

---

### 5.10 About

#### GET /about.json
Récupérer les informations sur l'API et les services disponibles.

**Response (200):**
```json
{
  "client": {
    "host": "192.168.1.10"
  },
  "server": {
    "current_time": 1705752000,
    "services": [
      {
        "name": "GitHub",
        "actions": [
          {
            "name": "new_issue",
            "description": "Triggered when a new issue is created"
          }
        ],
        "reactions": [
          {
            "name": "create_issue",
            "description": "Create a new issue"
          }
        ]
      }
    ]
  }
}
```

---

## 6. Authentification et OAuth

### 6.1 Flux d'Authentification JWT

#### Inscription
```
Client -> POST /api/register
       -> Hachage du mot de passe (bcrypt)
       -> Création utilisateur en BDD
       -> Retour des infos utilisateur
```

#### Connexion
```
Client -> POST /api/login
       -> Vérification email
       -> Comparaison mot de passe (bcrypt)
       -> Génération JWT (1h de validité)
       -> Retour token + userID
```

#### Utilisation du Token
```
Client -> Headers: { Authorization: "Bearer <token>" }
       -> Middleware vérifie le token
       -> Extraction des données (email)
       -> Autorisation de la requête
```

---

### 6.2 Flux OAuth 2.0

#### Exemple avec Google

**1. Initiation**
```
Client -> GET /api/auth/google?userId=1&plateforme=web
       -> Génération URL d'autorisation
       -> Redirection vers Google
```

**2. Autorisation**
```
User -> Autorise l'application sur Google
     -> Google redirige vers /api/auth/google/callback?code=...&state=...
```

**3. Échange de Token**
```
Server -> POST https://oauth2.googleapis.com/token
       -> Body: {
            client_id,
            client_secret,
            code,
            redirect_uri,
            grant_type: "authorization_code"
          }
       -> Réponse: { access_token, refresh_token }
```

**4. Récupération du Profil**
```
Server -> GET https://www.googleapis.com/oauth2/v2/userinfo
       -> Headers: { Authorization: "Bearer <access_token>" }
       -> Réponse: { email, id, ... }
```

**5. Stockage**
```
Server -> Mise à jour user.info.google
       -> {
            provider: "google",
            providerId,
            accessToken,
            refreshToken,
            email,
            isConnected: true
          }
       -> Sauvegarde en BDD
```

**6. Redirection**
```
Server -> Redirection vers frontend/mobile
       -> Web: http://localhost:8081/services
       -> Mobile: areafrontendmobile://auth/callback?provider=google&success=true&userId=1
```

---

### 6.3 Scopes OAuth par Provider

#### GitHub
```javascript
scope: "user:email"
```

#### Google
```javascript
scope: [
  "email",
  "profile",
  "https://www.googleapis.com/auth/drive",
  "https://www.googleapis.com/auth/calendar",
  "https://www.googleapis.com/auth/youtube",
  "https://mail.google.com/"
]
```

#### LinkedIn
```javascript
scope: "openid profile email w_member_social"
```

#### Discord
```javascript
scope: "identify email"
```

#### Microsoft
```javascript
scope: [
  "openid",
  "profile",
  "offline_access",
  "User.Read",
  "Mail.Read",
  "Mail.Send",
  "Calendars.ReadWrite",
  "Team.ReadBasic.All",
  "Channel.ReadBasic.All",
  "ChannelMessage.Send"
]
```

#### Twitter
```javascript
scope: "tweet.read tweet.write users.read follows.read follows.write offline.access"
```

#### Twitch
```javascript
scope: "user:read:email channel:read:subscriptions chat:read chat:edit"
```

#### Notion
```javascript
owner: "user"
```

#### Trello
```javascript
scope: "read:me read:account offline_access read:board:trello write:board:trello read:card:trello write:card:trello"
```

---

### 6.4 Rafraîchissement des Tokens

Les tokens OAuth expirent après un certain temps. Le système implémente un mécanisme de rafraîchissement automatique.

**Endpoint:**
```
POST /api/refresh
Body: {
  "userId": 1,
  "provider": "google"
}
```

**Processus:**
1. Récupération du refresh_token de l'utilisateur
2. Requête au provider pour obtenir un nouveau access_token
3. Mise à jour en base de données
4. Retour du nouveau token

**Token Scheduler:**
Le fichier `Module/TokenScheduler.js` vérifie périodiquement les tokens expirés et les rafraîchit automatiquement.

---

## 7. Services Intégrés

### 7.1 GitHub

**Module:** `Module/ServicesManagement/Github/`

#### Actions Disponibles
- **new_issue**: Nouveau problème créé
- **new_pull_request**: Nouvelle pull request
- **push**: Push sur un repository
- **new_repo**: Nouveau repository créé

#### Réactions Disponibles
- **create_issue**: Créer un problème
- **create_pull_request**: Créer une pull request
- **add_comment**: Ajouter un commentaire

#### Exemple de Workflow
```json
{
  "action": "new_issue",
  "action_params": {
    "repo": "username/repository"
  },
  "reaction": "send_email",
  "reaction_params": {
    "to": "admin@example.com",
    "subject": "New GitHub Issue",
    "body": "Issue: {{issue_title}}"
  }
}
```

---

### 7.2 Google Calendar

**Module:** `Module/ServicesManagement/Google_Calendar/`

#### Actions Disponibles
- **event_start**: Un événement commence
- **event_end**: Un événement se termine
- **new_event**: Nouvel événement créé
- **event_in_x_minutes**: Événement dans X minutes

#### Réactions Disponibles
- **create_event**: Créer un événement
- **update_event**: Modifier un événement
- **delete_event**: Supprimer un événement

---

### 7.3 Gmail

**Module:** `Module/ServicesManagement/Gmail/`

#### Actions Disponibles
- **new_email**: Nouvel email reçu
- **email_marked_read**: Email marqué comme lu
- **email_starred**: Email marqué important

#### Réactions Disponibles
- **send_email**: Envoyer un email
- **mark_read**: Marquer comme lu
- **delete_email**: Supprimer un email
- **star_email**: Marquer comme important

---

### 7.4 Discord

**Module:** `Module/ServicesManagement/Discord/`

#### Actions Disponibles
- **new_member**: Nouveau membre dans le serveur
- **new_message**: Nouveau message

#### Réactions Disponibles
- **send_message**: Envoyer un message
- **create_channel**: Créer un canal

---

### 7.5 LinkedIn

**Module:** `Module/ServicesManagement/Linkedin/`

#### Réactions Disponibles
- **create_post**: Créer une publication

---

### 7.6 Microsoft Calendar

**Module:** `Module/ServicesManagement/Microsoft_Calendar/`

#### Actions Disponibles
- **event_start**: Événement commence
- **event_end**: Événement se termine
- **new_event**: Nouvel événement

---

### 7.7 Outlook

**Module:** `Module/ServicesManagement/Outlook/`

#### Réactions Disponibles
- **send_email**: Envoyer un email via Outlook

---

### 7.8 Notion

**Module:** `Module/ServicesManagement/Notion/`

#### Réactions Disponibles
- **create_page**: Créer une page
- **update_task**: Mettre à jour une tâche

---

### 7.9 Timer (Service Interne)

#### Actions Disponibles
- **specific_time**: À une heure précise (HH:MM)
- **specific_date**: À une date précise (DD/MM)
- **days_until**: X jours avant un jour de la semaine
- **is_weekday**: Si c'est un jour de semaine

