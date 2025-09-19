# Documentation MSPR Microservices

## 📚 Index de la Documentation

### Guides Principaux
- **[README Principal](../README.md)** - Vue d'ensemble du projet
- **[Guide Frontend](frontend-guide.md)** - Utilisation et développement du frontend React
- **[Guide de Test](testing-guide.md)** - Tests automatisés et manuels
- **[Guide de Déploiement](deployment-guide.md)** - Déploiement local et production

### Documentation Technique
- **[Journal des Actions](WORKLOG.md)** - Historique des développements
- **[Architecture](architecture.md)** - Architecture détaillée du système
- **[API Documentation](api-docs.md)** - Documentation des APIs REST

## 🚀 Démarrage Rapide

### 1. Installation
```bash
git clone --recursive https://github.com/kuetcheal/mspr-microservices.git
cd mspr-microservices
docker-compose up -d
```

### 2. Accès aux Services
- **Frontend** : http://localhost:3001 (admin/admin)
- **Grafana** : http://localhost:3000 (admin/admin)
- **Prometheus** : http://localhost:9090
- **phpMyAdmin** : http://localhost:8085 (root/root)

### 3. Test Rapide
1. Ouvrir http://localhost:3001/products
2. Se connecter avec admin/admin
3. Vérifier l'affichage des produits avec images

## 🏗️ Architecture

### Services
- **service-client** : Authentification JWT, gestion des clients
- **service-produit** : Catalogue produits avec images
- **service-commande** : Gestion des commandes et facturation
- **frontend** : Interface React avec authentification

### Infrastructure
- **Bases de données** : MySQL (3 instances)
- **Message Broker** : RabbitMQ
- **Monitoring** : Prometheus + Grafana + Alertmanager
- **Conteneurisation** : Docker + Docker Compose

## 🛠️ Développement

### Structure du Projet
```
mspr-microservices/
├── services/               # Microservices Java
│   ├── service-client/     # Service d'authentification
│   ├── service-produit/    # Service de produits
│   └── service-commande/   # Service de commandes
├── frontend/               # Interface React
├── monitoring/             # Configuration Prometheus/Grafana
├── .github/workflows/      # CI/CD GitHub Actions
└── docs/                  # Documentation
```

### Commandes Utiles
```bash
# Démarrer tous les services
docker-compose up -d

# Vérifier le statut
docker-compose ps

# Logs d'un service
docker logs service_client

# Redémarrer un service
docker-compose restart service_client

# Mettre à jour les sous-modules
git submodule update --remote
```

## 🧪 Tests

### Tests Automatisés
- **CI/CD** : GitHub Actions avec tests Java et Node.js
- **Tests unitaires** : Maven pour Java, Jest pour React
- **Tests d'intégration** : Testcontainers avec RabbitMQ

### Tests Manuels
- **APIs** : Postman ou curl
- **Frontend** : Tests de navigation et fonctionnalités
- **Monitoring** : Vérification des métriques dans Grafana

## 📊 Monitoring

### Métriques Disponibles
- **JVM** : Mémoire, CPU, threads
- **HTTP** : Requêtes, temps de réponse, erreurs
- **Base de données** : Connexions, requêtes
- **RabbitMQ** : Messages, queues, consumers

### Dashboards Grafana
- **Application Overview** : Vue d'ensemble des services
- **JVM Metrics** : Métriques JVM détaillées
- **HTTP Metrics** : Métriques des APIs REST
- **Infrastructure** : CPU, mémoire, disque

## 🔧 Configuration

### Variables d'Environnement
- **Frontend** : VITE_API_BASE_URL_*
- **Backend** : SPRING_DATASOURCE_*, JWT_SECRET
- **Monitoring** : PROMETHEUS_*, GRAFANA_*

### Ports
- **Frontend** : 3001
- **APIs** : 8074, 8075, 8076
- **Monitoring** : 3000, 9090, 9093
- **Bases de données** : 3307, 3308, 3309

## 🚨 Dépannage

### Problèmes Courants
1. **Services non démarrés** : Vérifier `docker-compose ps`
2. **Erreurs CORS** : Vérifier la configuration SecurityConfig.java
3. **Images non affichées** : Vérifier les URLs dans la base de données
4. **Erreurs d'authentification** : Vérifier le token JWT

### Logs
```bash
# Logs de tous les services
docker-compose logs

# Logs d'un service spécifique
docker-compose logs service_client

# Logs en temps réel
docker-compose logs -f frontend
```

## 📈 Performance

### Métriques de Référence
- **Temps de réponse API** : < 200ms
- **Temps de chargement frontend** : < 2s
- **Utilisation mémoire** : < 512MB par service
- **Disponibilité** : > 99.9%

### Optimisations
- **Caching** : Redis pour les sessions
- **CDN** : CloudFlare pour les assets
- **Load Balancing** : Nginx/HAProxy
- **Database** : Indexes optimisés

## 🔄 CI/CD

### Pipeline GitHub Actions
1. **Détection** : Changements dans les services
2. **Build** : Compilation Java et Node.js
3. **Test** : Tests unitaires et d'intégration
4. **Déploy** : Build et push des images Docker

### Branches
- **master** : Branche principale de production
- **develop** : Branche de développement
- **feature/*** : Branches de fonctionnalités

## 📝 Contribution

### Workflow
1. Fork du repository
2. Création d'une branche feature
3. Développement et tests
4. Pull request vers develop
5. Review et merge

### Standards
- **Code** : Java 21, React 18, TypeScript
- **Tests** : Couverture > 80%
- **Documentation** : README mis à jour
- **Commits** : Messages clairs et descriptifs

## 📞 Support

### Ressources
- **Documentation** : Ce dossier docs/
- **Issues** : GitHub Issues
- **Discussions** : GitHub Discussions
- **Wiki** : GitHub Wiki

### Contact
- **Développeur** : [@kuetcheal](https://github.com/kuetcheal)
- **Repository** : [mspr-microservices](https://github.com/kuetcheal/mspr-microservices)
