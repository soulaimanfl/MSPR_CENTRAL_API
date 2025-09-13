# MSPR Microservices - Architecture Centralisée

## 🏗️ Architecture

Ce repo central orchestre les 3 services microservices de PayeTonKawa :

- **service-client** : Gestion des clients (port 8074)
- **service-produit** : Gestion des produits (port 8075) 
- **service-commande** : Gestion des commandes (port 8076)

## 🚀 Services

| Service | Port | Description | Repo |
|---------|------|-------------|------|
| Client | 8074 | Gestion des clients, authentification JWT | [service_client](https://github.com/kuetcheal/service_client) |
| Produit | 8075 | Gestion du catalogue produits | [service_produit](https://github.com/kuetcheal/service_produit) |
| Commande | 8076 | Gestion des commandes et facturation | [service_commande](https://github.com/kuetcheal/service_commande) |

## 🛠️ Stack Technique

- **Backend** : Spring Boot 3.5.3 + Java 21
- **Base de données** : MySQL 8.0 (3 instances)
- **Message Broker** : RabbitMQ
- **Monitoring** : Prometheus + Grafana + Alertmanager
- **Conteneurisation** : Docker + Docker Compose
- **CI/CD** : GitHub Actions

## 🚀 Démarrage Rapide

```bash
# Cloner le repo avec sous-modules
git clone --recursive https://github.com/kuetcheal/mspr-microservices.git
cd mspr-microservices

# Lancer tous les services
docker-compose up -d

# Vérifier le statut
docker-compose ps
```

## 📊 Monitoring

- **Grafana** : http://localhost:3000 (admin/admin)
- **Prometheus** : http://localhost:9090
- **RabbitMQ Management** : http://localhost:15672 (guest/guest)

## 🔧 Développement

### Structure du projet
```
mspr-microservices/
├── services/
│   ├── service-client/     # Sous-module Git
│   ├── service-produit/    # Sous-module Git
│   └── service-commande/   # Sous-module Git
├── .github/workflows/      # Pipelines CI/CD
├── docker-compose.yml      # Orchestration des services
├── monitoring/             # Configs Prometheus/Grafana
└── docs/                  # Documentation
```

### Mise à jour des sous-modules
```bash
# Mettre à jour tous les sous-modules
git submodule update --remote

# Mettre à jour un sous-module spécifique
git submodule update --remote services/service-client
```

## 🚀 CI/CD

Le pipeline central détecte automatiquement les changements dans chaque service et :
1. **Build** uniquement les services modifiés
2. **Test** les services affectés
3. **Déploie** les images Docker
4. **Met à jour** l'infrastructure

## 📝 Documentation

- [Guide de développement](docs/development.md)
- [Architecture détaillée](docs/architecture.md)
- [Guide de déploiement](docs/deployment.md)
