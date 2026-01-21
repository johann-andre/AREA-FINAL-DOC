# README — Projet AREA (Global)

## Table des matières

1. Présentation du projet  
2. Équipe  
3. Architecture globale  
4. Technologies  
5. Structure des dépôts  
6. Installation complète  
7. Backend — API & Moteur AREA  
8. Front-End Web  
9. Application Mobile  
10. Sécurité et Authentification  
11. Flux OAuth  
12. Système de Workflows (AREA)  
13. Tests  
14. Limites du MVP  
15. Perspectives d’amélioration  
16. Post-mortem global  
17. Conclusion  

---

## 1. Présentation du projet

AREA (Action–Reaction) est une plateforme d’automatisation inspirée d’IFTTT permettant de connecter différents services entre eux.

Lorsqu’un événement (Action) se produit sur un service, une Réaction est automatiquement déclenchée sur un autre service.

Exemples :
- Nouveau commit GitHub → notification Discord
- Nouveau mail Gmail → création d’une tâche Notion
- Nouvel événement calendrier → message Teams

Le projet est composé de :
- une API Backend (moteur d’automatisation),
- un Front-End Web,
- une Application Mobile.

---

## 2. Équipe

### Front-End Web
- Johann-André MAFORIKAN  
- Emeric AMEGAH  

### Backend
- Darren HOUNSA
- Hilary ZOCLI

### Mobile
- Wakil KARIMOU
---

## 3. Architecture globale

```text
[ Web App (React) ]        [ Mobile App (Expo) ]
           \                 //
            \               //
             v              v
                [ API Backend AREA ]
                         |
                         v
                 [ Services Externes ]
          (Google, GitHub, Discord, etc.)
```

Rôles :
- Front Web : configuration des workflows, gestion UI.
- Mobile : gestion rapide des services et workflows.
- Backend : orchestration, polling, webhooks, OAuth, exécution des réactions.

---

## 4. Technologies

### Backend
- Node.js
- Express
- Sequelize (ORM)
- MySQL / PostgreSQL
- OAuth 2.0
- JWT
- Docker

### Front-End Web
- React
- Axios
- Yup
- CSS natif
- Jest

### Mobile
- React Native
- Expo
- Axios
- AsyncStorage
- Expo Router

---

## 5. Structure des dépôts

### Backend
```
AREA-Backend/
├── Api/Controllers
├── Api/Routes
├── Database/models
├── Module/ServicesManagement
├── Module/TokenScheduler.js
└── server.js
```

### Front-End Web
```
area_frontend_web/
├── src/Pages
├── src/Components
├── src/services
├── src/Utils
└── src/tests
```

### Mobile
```
area_frontend_mobile/
├── app/ (routing Expo)
├── components
├── services
├── context
└── constants
```

---

## 6. Installation complète

### Backend
```bash
cd AREA-Backend
npm install
npm start
```

### Front-End Web
```bash
cd area_frontend_web
npm install
npm start
```

### Mobile
```bash
cd area_frontend_mobile
npm install
npx expo start
```

Configurer les IP locales dans les fichiers API si nécessaire.

---

## 7. Backend — API & Moteur AREA

Fonctions principales :
- gestion des utilisateurs
- gestion des services
- gestion des actions et réactions
- création de workflows
- polling / webhooks
- exécution des réactions

Le moteur vérifie périodiquement :
- si une action est déclenchée
- si la condition est remplie
- puis exécute la réaction correspondante

Un scheduler permet également de rafraîchir automatiquement les tokens OAuth.

---

## 8. Front-End Web

Fonctionnalités :
- authentification
- connexion OAuth
- création d’AREA via builder
- utilisation de templates
- édition de workflows
- bibliothèque des automatisations

Architecture :
- Pages : écrans complets
- Components : UI réutilisable
- services : accès API
- Utils : validation Yup

---

## 9. Application Mobile

Fonctionnalités :
- authentification
- connexion services
- création d’AREA
- activation/désactivation workflows

Spécificités :
- deep linking OAuth
- stockage sécurisé JWT
- routing basé sur fichiers (Expo Router)

---

## 10. Sécurité et Authentification

- Authentification via JWT
- Tokens stockés côté client
- Vérification middleware backend
- Tokens OAuth stockés uniquement backend
- Rafraîchissement automatique des tokens

---

## 11. Flux OAuth

1. Client demande connexion service
2. Backend redirige vers provider
3. Provider renvoie code
4. Backend échange contre tokens
5. Stockage BDD
6. Redirection client

Fonctionne pour :
- Google
- GitHub
- Discord
- Microsoft
- LinkedIn
- Twitch
- Notion
- Trello

---

## 12. Système de Workflows (AREA)

Chaque workflow contient :
- 1 Action
- 1 Reaction
- paramètres dynamiques

Processus :
1. Déclenchement action
2. Vérification condition
3. Exécution réaction
4. Mise à jour du dernier état

Les workflows peuvent être :
- activés
- désactivés
- modifiés
- supprimés

---

## 13. Tests

### Front-End
- Jest
- React Testing Library

### Backend
- Tests manuels API
- Scénarios OAuth

Tests E2E complets non inclus dans MVP.

---

## 14. Limites du MVP

- peu de monitoring
- pas de dashboard analytics avancé
- peu de tests E2E
- UI perfectible sur mobile

---

## 15. Perspectives d’amélioration

- notifications temps réel
- moteur règles conditionnelles
- marketplace de templates
- analytics workflows
- multi-actions par workflow

---

## 16. Post-mortem global

### Points positifs
- architecture modulaire
- communication front/back
- moteur fonctionnel
- multi-plateformes

### Difficultés
- complexité OAuth
- synchronisation équipes
- délais tests

### Enseignements
- importance contrats API
- besoin de mocks backend
- gestion projet essentielle

---

## 17. Conclusion

Le projet AREA démontre la faisabilité d’une plateforme complète d’automatisation multi-services.

Le MVP couvre :
- la configuration utilisateur
- la connexion aux services
- l’exécution automatique des réactions

La base technique est solide et permet des évolutions futures.
