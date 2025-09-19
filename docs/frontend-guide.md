# Guide Frontend React

## 🚀 Démarrage

### Accès
- **URL** : http://localhost:3001
- **Identifiants** : admin / admin

### Fonctionnalités

#### 🔐 Authentification
- **Connexion** : Page `/login`
- **Déconnexion** : Automatique après 1 heure
- **Protection** : Toutes les pages nécessitent une authentification

#### 🛍️ Catalogue Produits
- **Page** : `/products`
- **Fonctionnalités** :
  - Affichage des produits avec images
  - Ajout au panier
  - Gestion du stock
  - Prix formatés en euros

#### 🛒 Panier
- **Page** : `/cart`
- **Fonctionnalités** :
  - Ajout/suppression d'articles
  - Calcul du total
  - Validation de commande

## 🛠️ Développement

### Structure
```
frontend/
├── src/
│   ├── components/         # Composants réutilisables
│   │   ├── common/        # Composants communs
│   │   └── layout/        # Layout (Navbar, etc.)
│   ├── pages/             # Pages de l'application
│   ├── api/               # Appels API
│   ├── context/           # Contextes React (Auth, Cart)
│   └── App.jsx            # Composant principal
├── public/                # Assets statiques
├── Dockerfile             # Configuration Docker
└── package.json           # Dépendances
```

### Variables d'environnement
```bash
VITE_API_BASE_URL_CLIENT=http://localhost:8074/api
VITE_API_BASE_URL_PRODUIT=http://localhost:8075/api
VITE_API_BASE_URL_COMMANDE=http://localhost:8076/api
```

### Commandes de développement
```bash
# Installation des dépendances
npm install

# Démarrage en mode développement
npm run dev

# Build de production
npm run build

# Build avec Docker
docker-compose up -d --build frontend
```

## 🔧 Configuration

### Authentification
- **Token JWT** : Stocké dans localStorage
- **Expiration** : 1 heure
- **Redirection** : Automatique vers `/login` si non connecté

### API
- **Base URL** : Configurée via variables d'environnement
- **Headers** : Authorization Bearer automatique
- **Gestion d'erreurs** : Redirection automatique en cas d'erreur 401

### Images
- **Source** : URLs externes (Unsplash)
- **Fallback** : Message "Pas d'image" si URL invalide
- **Optimisation** : Redimensionnement automatique (400x400)

## 🐛 Dépannage

### Problèmes courants

#### 1. Page blanche
- **Cause** : Service backend non démarré
- **Solution** : Vérifier `docker-compose ps`

#### 2. Erreur 403 sur les APIs
- **Cause** : Non authentifié
- **Solution** : Se connecter avec admin/admin

#### 3. Images ne s'affichent pas
- **Cause** : URLs d'images invalides
- **Solution** : Vérifier la base de données

#### 4. CORS errors
- **Cause** : Configuration CORS incorrecte
- **Solution** : Vérifier les ports dans SecurityConfig.java

### Logs
```bash
# Logs du frontend
docker logs webshop_frontend

# Logs des services
docker logs service_client
docker logs service_produit
docker logs service_commande
```

## 📱 Responsive Design

Le frontend est optimisé pour :
- **Desktop** : 1200px+
- **Tablet** : 768px - 1199px
- **Mobile** : < 768px

## 🎨 Thème

- **Framework** : Material-UI
- **Couleurs** : Palette bleue professionnelle
- **Typographie** : Roboto
- **Icônes** : Material Icons
