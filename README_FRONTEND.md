# README — Front-End Web — Projet AREA

## Table des matières
1. [Présentation](#1-présentation)
2. [Équipe Front-End](#2-équipe-front-end)
3. [Périmètre du Front-End](#3-périmètre-du-front-end)
4. [Technologies](#4-technologies)
5. [Prérequis](#5-prérequis)
6. [Installation et lancement](#6-installation-et-lancement)
7. [Configuration](#7-configuration)
8. [Architecture du projet](#8-architecture-du-projet)
9. [Routing et navigation](#9-routing-et-navigation)
10. [Flow utilisateur](#10-flow-utilisateur)
11. [Pages et responsabilités](#11-pages-et-responsabilités)
12. [Couche d’accès API](#12-couche-daccès-api)
13. [Validation des formulaires](#13-validation-des-formulaires)
14. [Gestion des états et UX](#14-gestion-des-états-et-ux)
15. [Tests](#15-tests)
16. [Sécurité côté Front](#16-sécurité-côté-front)
17. [Limites du MVP](#17-limites-du-mvp)
18. [Améliorations prévues](#18-améliorations-prévues)
19. [Contribution](#19-contribution)
20. [Conclusion](#20-conclusion)

---

## 1. Présentation
Ce dépôt contient le client web du projet **AREA (Automated Reaction Event Application)**.

Le front-end permet aux utilisateurs de :
- créer un compte et se connecter ;
- connecter des services via OAuth ;
- créer des automatisations (workflows AREA) ;
- consulter, modifier et supprimer leurs workflows ;
- utiliser des templates (workflows prédéfinis).

Le front-end communique avec le backend via une API REST. Toute la logique d’exécution des automatisations est gérée côté backend.

---

## 2. Équipe Front-End
Responsables :
- **Johann-André MAFORIKAN** — UI, pages, routing, intégration visuelle, UX
- **Emeric AMEGAH** — services API, validation formulaires, logique métier front, intégration backend

---

## 3. Périmètre du Front-End
Le front-end web couvre :
- l’interface utilisateur (pages, composants, navigation) ;
- la gestion de session côté client (stockage du JWT) ;
- les appels API vers le backend (auth, services, workflows) ;
- la configuration des workflows (builder CreateArea, templates).

Le front-end **ne** :
- stocke pas de tokens OAuth de fournisseurs externes ;
- n’exécute pas les actions/réactions ; il configure et déclenche uniquement des appels API.

---

## 4. Technologies
- **React** (Create React App)
- **Axios** (requêtes HTTP)
- **Yup** (validation des formulaires)
- **CSS** (responsive + animations)
- **Jest** + **React Testing Library** (tests unitaires)

---

## 5. Prérequis
- Node.js >= 18
- npm >= 9

---

## 6. Installation et lancement
### Installation
```bash
npm install
```

### Lancement en développement
```bash
npm start
```

Application accessible sur :
- http://localhost:3000

### Build production
```bash
npm run build
```

---

## 7. Configuration
### Variables d’environnement (recommandé)
Créer un fichier `.env` à la racine du front (si votre projet les utilise) :

```env
REACT_APP_API_BASE_URL=http://localhost:8080
REACT_APP_OAUTH_REDIRECT_URI=http://localhost:3000/callback
```

Si le projet utilise une baseURL codée côté `services/axios.js`, adaptez-la selon l’environnement (dev / prod).

---

## 8. Architecture du projet
### Arborescence (principale)
```text
area_frontend_web/
├── package.json
├── package-lock.json
├── public/
│   ├── favicon.ico
│   └── index.html
└── src/
    ├── App.js
    ├── App.css
    ├── index.js
    ├── index.css
    ├── Components/
    │   ├── atoms/
    │   ├── molecules/
    │   ├── organisms/
    │   └── templates/
    ├── Pages/
    │   ├── Home/              (Home.js, Home.css)
    │   ├── Login/             (Login.js, Login.css)
    │   ├── Register/          (Register.js, Register.css)
    │   ├── Dashboard/         (Dashboard.js, Dashboard.css)
    │   ├── Service/           (Service.js, Service.css)
    │   ├── ServiceDetail/     (ServiceDetail.js, ServiceDetail.css)
    │   ├── Templates/         (Templates.js, Templates.css)
    │   ├── UseTemplate/       (UseTemplate.js, UseTemplate.css)
    │   ├── CreateArea/        (CreateArea.js, CreateArea.css)
    │   ├── AddService/        (AddService.js, AddService.css)
    │   ├── AddAction/         (AddAction.js, AddAction.css)
    │   ├── AddReaction/       (AddReaction.js, AddReaction.css)
    │   ├── Library/           (Library.js, Library.css)
    │   ├── EditWorkflow/      (EditWorkflow.js, EditWorkflow.css)
    │   ├── Profil/            (Profil.js, Profil.css)
    │   └── admin/             (Admin.js, admin.css)
    ├── services/
    │   ├── axios.js
    │   ├── authService.js
    │   └── serviceService.js
    ├── Utils/
    │   └── validationSchemas.js
    └── tests/
        ├── Accordion.test.js
        ├── Button.test.js
        ├── Card.test.js
        ├── Checkbox.test.js
        ├── Icon.test.js
        └── CreateArea.test.js
```

### Logique d’organisation
- **Pages/** : écrans complets (un dossier = une page, avec `.js` et `.css`).
- **Components/** : composants réutilisables (approche Atomic Design : atoms → molecules → organisms → templates).
- **services/** : point d’entrée unique pour les appels API (Axios + fonctions métier).
- **Utils/** : utilitaires transverses (validation, helpers).
- **tests/** : tests unitaires sur composants et page centrale (CreateArea).

---


---

## Schéma d’architecture globale (vue simplifiée)

```text
[ Utilisateur (Navigateur) ]
            |
            v
[ Front-End Web (React) ]
            |
            |  Requêtes HTTP (Axios + JWT)
            v
[ API Backend AREA ]
            |
            |  Webhooks / OAuth / Jobs
            v
[ Services Externes ]
(Gmail, Discord, GitHub, etc.)
```

Rôle du Front-End :
- collecte les choix utilisateur (services, actions, réactions, paramètres)
- envoie la configuration au backend
- affiche l’état des workflows
- ne gère jamais l’exécution des automatisations

Toute la logique d’orchestration, de planification et d’exécution est déportée côté backend.


## 9. Routing et navigation
Le routing est centralisé dans `App.js` :
- association des routes URL vers les pages ;
- gestion des redirections ;
- mise en place éventuelle d’un layout commun.

Routes typiques (selon configuration) :
- `/` : Home
- `/login` : Login
- `/register` : Register
- `/dashboard` : Dashboard
- `/services` : Service
- `/services/:id` : ServiceDetail
- `/templates` : Templates
- `/use-template/:id` : UseTemplate
- `/create-area` : CreateArea
- `/library` : Library
- `/edit-workflow/:id` : EditWorkflow
- `/profile` : Profil
- `/admin` : Admin

---

## 10. Flow utilisateur
1. Arrivée sur Home
2. Inscription (Register) ou connexion (Login)
3. Dashboard
4. Connexion des services (Services → ServiceDetail → OAuth)
5. Création d’un workflow :
   - mode complet : CreateArea
   - mode rapide : Templates → UseTemplate
6. Consultation des workflows : Library
7. Modification : EditWorkflow
8. Gestion du compte : Profil (déconnexion)

---

## 11. Pages et responsabilités
### Pages publiques
- **Home** : landing page (présentation du projet).
- **Login** : connexion (JWT).
- **Register** : création de compte.

### Pages applicatives
- **Dashboard** : vue globale et accès rapide aux actions (création/gestion).
- **Service** : liste des services connectables.
- **ServiceDetail** : détail d’un service et accès OAuth.
- **CreateArea** : builder complet d’un workflow (Action → Reaction + paramètres).
- **Library** : liste des workflows de l’utilisateur.
- **EditWorkflow** : modification d’un workflow existant.
- **Profil** : informations utilisateur et logout.

### Templates et accélérateurs
- **Templates** : liste de workflows prédéfinis.
- **UseTemplate** : configuration de paramètres d’un template et création du workflow.

### Pages avancées / administration
- **AddService** : ajout d’un service (configuration OAuth) — usage interne/avancé.
- **AddAction** : ajout d’une action personnalisée — usage interne/avancé.
- **AddReaction** : ajout d’une réaction personnalisée — usage interne/avancé.
- **Admin** : dashboard administration (statistiques/supervision).

---

## 12. Couche d’accès API
### 12.1 services/axios.js
Objectif : centraliser la configuration HTTP.
- baseURL (backend)
- ajout automatique du header `Authorization: Bearer <token>`
- point unique pour améliorer la gestion des erreurs (401, 403, 500) et ajouter des interceptors.

### 12.2 services/authService.js
Fonctions typiques :
- `login(email, password)`
- `register(payload)`
- `getProfile()`
- `logout()`

Le token JWT est stocké côté navigateur (localStorage) afin de persister la session. Les pages utilisent ce service plutôt que d’appeler Axios directement.

### 12.3 services/serviceService.js
Responsabilités :
- récupérer la liste des services ;
- connaître le statut (connecté/non connecté) ;
- déclencher les flux OAuth (redirection vers le backend).

### Endpoints généralement utilisés (résumé)
Les endpoints exacts peuvent varier selon le backend, mais le front utilise typiquement :
- Auth : `POST /api/login`, `POST /api/register`, `GET /api/me`
- Services : `GET /api/services`, `GET /api/services/:id`, `GET/POST /api/oauth/...`
- Workflows : `GET /api/areas`, `POST /api/areas`, `PUT /api/areas/:id`, `DELETE /api/areas/:id`
- Templates : `GET /api/templates`, `GET /api/templates/:id`

---

## 13. Validation des formulaires
La validation est centralisée dans `Utils/validationSchemas.js` avec Yup.

Formulaires couverts (selon implémentation) :
- Login
- Register
- CreateArea (paramètres de workflow)
- AddAction / AddReaction (pages avancées)

Avantages :
- messages d’erreur cohérents ;
- logique séparée de l’UI ;
- maintenance simplifiée.

---

## 14. Gestion des états et UX
Les pages gèrent les états suivants :
- **loading** : récupération en cours (API) ;
- **empty** : absence de données (liste de workflows vide, etc.) ;
- **error** : erreur réseau/serveur (message explicite).

Cette gestion évite les pages blanches et améliore la compréhension côté utilisateur.

---

## 15. Tests
Des tests unitaires existent pour :
- composants UI (Button, Card, Checkbox, Icon, Accordion) ;
- page centrale (CreateArea).

Exécution (si script présent) :
```bash
npm test
```

Objectifs :
- garantir le rendu des composants critiques ;
- vérifier des interactions utilisateur ;
- limiter les régressions sur le builder (CreateArea).

---

## 16. Sécurité côté Front
- JWT stocké dans le navigateur (localStorage).
- Le token est envoyé dans les headers via Axios.
- Aucun token OAuth externe n’est conservé côté front (géré par le backend).
- Les pages sensibles devraient être protégées (voir section améliorations).

---

## 17. Limites du MVP
- ProtectedRoute / protection de navigation côté client perfectible (selon implémentation actuelle).
- Pas de refresh token automatique.
- Tests E2E non inclus (focus tests unitaires).
- Gestion des erreurs serveur peut être enrichie (toasts, codes d’erreur, fallback UI).

---

## 18. Améliorations prévues
- ProtectedRoute centralisé (redirection automatique vers `/login`).
- Notifications globales (toast) pour succès/erreurs.
- Interceptors Axios : gestion 401, refresh token, retry.
- Tests E2E (Cypress).
- Accessibilité (navigation clavier, contrastes, ARIA).
- Amélioration du monitoring des erreurs (log/telemetry).

---

## 19. Contribution
Recommandations pour contribuer efficacement :
- une branche par fonctionnalité ;
- commits courts et descriptifs ;
- éviter les appels API directs dans les pages (passer par `services/`) ;
- garder la validation dans `validationSchemas.js` ;
- conserver la structure “1 page = 1 dossier” dans `Pages/`.

---


---

## Post-mortem Front-End

### Ce qui a bien fonctionné

- Architecture claire dès le départ (séparation Pages / Components / Services).
- Bonne coordination avec l’équipe backend pour définir les endpoints.
- Builder CreateArea fonctionnel permettant la création complète d’un workflow.
- Mise en place de validation centralisée pour éviter les incohérences UX.
- Présence de tests unitaires sur les composants critiques.

### Ce qui a été difficile

- Synchronisation entre l’avancement backend et frontend sur certains endpoints.
- Gestion des flux OAuth dépendante du backend, rendant le debug plus complexe côté front.
- Complexité de la page CreateArea (beaucoup d’états, dépendances entre choix).
- Temps limité pour mettre en place des tests E2E complets.

### Ce que nous ferions différemment

- Définir plus tôt un contrat API formel (OpenAPI/Swagger).
- Mettre en place des mocks backend pour le développement front.
- Ajouter un système global de gestion d’erreurs (toasts).
- Introduire plus tôt des routes protégées (ProtectedRoute).
- Prévoir davantage de temps pour les tests de bout en bout.

Ce retour d’expérience a permis de mieux comprendre l’importance :
- de la communication entre équipes,
- de la documentation des APIs,
- et de la séparation stricte des responsabilités entre front et back.


## 20. Conclusion
Le front-end web du projet AREA fournit une interface complète pour :
- l’authentification ;
- la connexion des services ;
- la création et la gestion de workflows (AREA) ;
- l’utilisation de templates.

L’architecture est modulaire et maintenable, adaptée à un MVP et prête pour des itérations supplémentaires.
