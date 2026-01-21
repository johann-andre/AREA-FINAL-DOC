# 📱 Area Mobile Frontend

![Status](https://img.shields.io/badge/Status-Development-yellow) ![Platform](https://img.shields.io/badge/Platform-Android%20%7C%20iOS-brightgreen) ![Tech](https://img.shields.io/badge/Tech-React%20Native%20%7C%20Expo-blue)

Application mobile officielle pour le projet **AREA** (Action-Reaction), une plateforme d'automatisation inspirée d'IFTTT (If This Then That). Développée avec **React Native** (framework Expo), elle offre une expérience fluide et native pour gérer vos services et automatisations.

---

## 📑 Table des Matières

1. [Fonctionnalités Détaillées](#-fonctionnalités-détaillées)
2. [Stack Technique](#-stack-technique)
3. [Prérequis Système](#-prérequis-système)
4. [Guide d'Installation](#-guide-dinstallation)
5. [Lancement et Développement](#-lancement-et-développement)
6. [Structure du Code](#-structure-du-code)
7. [Dépannage (Troubleshooting)](#-dépannage-troubleshooting)
8. [Génération d'APK](#-génération-dapk)

---

## 🌟 Fonctionnalités Détaillées

### 🔐 Authentification & Sécurité
- **Inscription & Connexion** : Interface utilisateur fluide avec validation de formulaire en temps réel (via `Yup`).
- **Gestion de Session** : Persistance de la connexion grâce au stockage sécurisé du token JWT sur l'appareil.
- **Redirection Automatique** : Détection automatique de l'état de connexion au lancement de l'application.

### 🧩 Gestion des Services (OAuth 2.0)
- **Connexion Services Tiers** : Intégration complète avec les flux OAuth 2.0 pour :
  - Google / Gmail
  - GitHub
  - Discord
  - Spotify
  - Twitch
  - Et bien d'autres...
- **Deep Linking** : Retour intelligent vers l'application après l'authentification sur le navigateur mobile.

### ⚡ Gestion des Areas (Workflows)
- **Tableau de Bord** : Vue d'ensemble de toutes les automatisations actives.
- **Créateur d'Automation** : Assistant étape par étape pour créer une nouvelle AREA :
  1. Choix du déclencheur (Trigger Service & Action).
  2. Configuration des paramètres du déclencheur.
  3. Choix de la réaction (Reaction Service & Reaction).
  4. Configuration des paramètres de la réaction.
- **CRUD Complet** : Possibilité d'activer, désactiver ou supprimer une Area existante.

### 🎨 Expérience Utilisateur (UX)
- **Design Adaptatif** : Interface optimisée pour toutes les tailles d'écrans (téléphones et tablettes).
- **Thèmes Dynamiques** : Support natif du **Mode Sombre (Dark Mode)** et du Mode Clair selon les préférences système de l'utilisateur.
- **Animations** : Transitions fluides entre les écrans et feedbacks visuels lors des interactions.

---

## 💻 Stack Technique

| Catégorie | Technologie | Version | Description |
|-----------|-------------|---------|-------------|
| **Core** | React Native | 0.81.5 | Framework principal pour le rendu natif. |
| **Platform** | Expo SDK | 54.0 | Suite d'outils simplifiant le développement React Native. |
| **Langage** | TypeScript | 5.x | Typage statique pour un code plus robuste. |
| **Routing** | Expo Router | 6.0 | Système de navigation basé sur les fichiers (type Next.js). |
| **HTTP Client** | Axios | 1.13 | Gestion des requêtes API avec intercepteurs. |
| **Validation** | Yup | 1.7 | Schémas de validation pour les formulaires. |
| **Storage** | Async Storage | 2.2 | Stockage de données persistant (tokens, cache simple). |
| **Icons** | Expo Vector Icons | 15.0 | Icônes vectorielles natives (Ionicons, FontAwesome, etc.). |

---

## ⚙️ Prérequis Système

- **Node.js** : Version 20 LTS ou supérieure.
- **Gestionnaire de paquets** : `npm` (inclus avec Node) ou `yarn`.
- **Environnement Mobile** :
  - **Android** : Android Studio (pour l'émulateur) ou un téléphone Android physique.
  - **iOS** : Xcode (macOS uniquement, pour le simulateur) ou un iPhone.
  - **Expo Go** : Application à installer sur votre téléphone pour tester sans compiler.

---

## � Guide d'Installation

1. **Cloner le projet complet** :
   ```bash
   git clone git@github.com:Epitech-2025/AREA.git
   cd AREA/area_frontend_mobile
   ```

2. **Installation des dépendances** :
   Le flag `--legacy-peer-deps` peut être nécessaire si des conflits de versions mineurs apparaissent avec Expo.
   ```bash
   npm install
   # OU
   yarn install
   ```

3. **Configuration de l'Environnement** :
   Vérifiez le fichier `services/api.ts`. Pour le développement local, vous devez souvent ajuster l'URL :
   ```typescript
   // Pour émulateur Android (alias localhost)
   export const API_URL = 'http://10.0.2.2:8080/api'; 
   
   // Pour téléphone physique (sur le même Wi-Fi)
   // Remplacez X.X par l'IP locale de votre PC (ex: 192.168.1.15)
   export const API_URL = 'http://192.168.X.X:8080/api';
   ```

---

## ▶️ Lancement et Développement

### Démarrer le Serveur de Développement
```bash
npx expo start
```
Cela lance Metro Bundler, le serveur qui compile le JavaScript pour votre téléphone.

### Commandes Utiles dans le Terminal Expo
- `a` : Ouvrir dans l'émulateur **Android** (doit être lancé au préalable).
- `i` : Ouvrir dans le simulateur **iOS** (macOS seulement).
- `w` : Ouvrir la version **Web** (limitée, utilisé pour debug rapide UI).
- `r` : Recharger l'application (Hot Reload).
- `m` : Afficher le menu de développement sur le téléphone.

---

## 📂 Structure du Code

Une architecture claire pour faciliter la maintenance :

```
area_frontend_mobile/
├── app/                       # 🚦 ROUTAGE (Expo Router)
│   ├── (tabs)/                # Routes protégées avec barre de navigation (Dashboard, etc.)
│   │   ├── _layout.tsx        # Configuration de la TabBar
│   │   ├── areas.tsx          # Écran "Mes Areas"
│   │   └── services.tsx       # Écran "Connexion Services"
│   ├── services/              # Routes dynamiques pour détails des services
│   ├── _layout.tsx            # Layout Racine (Providers globaux)
│   ├── index.tsx              # Page d'accueil (Landing)
│   ├── login.tsx              # Page de Login
│   └── register.tsx           # Page d'Inscription
├── components/                # 🧩 COMPOSANTS UI
│   ├── ui/                    # Composants primitifs (IconSymbol, etc.)
│   ├── Button.tsx             # Bouton standardisé (Primary/Secondary)
│   ├── Input.tsx              # Champ texte stylisé
│   └── ServiceCard.tsx        # Carte d'affichage d'un service (logo, état)
├── context/                   # 🧠 GESTION D'ÉTAT
│   └── AreaContext.tsx        # Contexte global pour les données Areas
├── services/                  # 🔌 LOGIQUE MÉTIER & API
│   ├── api.ts                 # Configuration Axios (Base URL, Headers)
│   └── authService.ts         # Toutes les méthodes d'appel API (Login, GetServices...)
├── hooks/                     # 🎣 CUSTOM HOOKS
│   └── useColorScheme.ts      # Gestion du thème (Dark/Light)
└── constants/                 # 🎨 CONSTANTES
    └── Colors.ts              # Palette de couleurs de l'application
```

---

## ❓ Dépannage (Troubleshooting)

### Erreur: "Network Error" ou "Comemction Refused"
- **Cause** : Le téléphone ne peut pas atteindre le serveur backend.
- **Solution** :
  1. Assurez-vous que le backend tourne (`docker-compose up` ou `npm start`).
  2. Vérifiez l'IP dans `services/api.ts`. `localhost` ne fonctionne **pas** sur un téléphone physique. Il faut l'IP locale de votre PC.
  3. Vérifiez que votre pare-feu autorise le port 8080.

### Erreur: "EACCES: permission denied"
- **Solution** : Lancez les commandes avec `sudo` ou réparez les permissions npm : `chown -R $USER ~/.npm`.

### Erreur: "SDK Version Mismatch"
- **Solution** : Votre version d'Expo Go sur le téléphone est peut-être trop ancienne ou trop récente. Mettez à jour l'app Expo Go via le Store, ou mettez à jour votre projet avec `npx expo install fix`.

---

## � Génération d'APK (Build)

Pour générer un fichier `.apk` installable sur Android sans passer par Expo Go :

1. Installer EAS CLI :
   ```bash
   npm install -g eas-cli
   ```
2. Se connecter à Expo :
   ```bash
   eas login
   ```
3. Configurer le build (déjà fait si `eas.json` existe) :
   ```bash
   eas build:configure
   ```
4. Lancer le build Android :
   ```bash
   eas build -p android --profile preview
   ```
   *Cela générera un lien pour télécharger l'APK après quelques minutes.*
