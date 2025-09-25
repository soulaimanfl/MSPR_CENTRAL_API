# 🚀 Guide de Démarrage pour Collègue

## Prérequis
- Docker Desktop installé
- Git installé

## 1. Cloner le projet avec submodules
```bash
git clone --recursive https://github.com/kuetcheal/mspr-microservices.git
cd mspr-microservices
```

## 2. Lancer tous les services
```bash
# Démarrer tous les services
docker-compose up -d

# Vérifier le statut
docker-compose ps

# Voir les logs
docker-compose logs -f
```

## 3. Accès aux services
- **Frontend** : http://localhost:3001 (admin/admin)
- **API Client** : http://localhost:8074/api
- **API Produit** : http://localhost:8075/api
- **API Commande** : http://localhost:8076/api
- **Grafana** : http://localhost:3000 (admin/admin)
- **Prometheus** : http://localhost:9090
- **RabbitMQ** : http://localhost:15672 (guest/guest)
- **phpMyAdmin** : http://localhost:8085 (root/root)

## 4. Commandes utiles
```bash
# Arrêter tous les services
docker-compose down

# Redémarrer un service spécifique
docker-compose restart service-client

# Voir les logs d'un service
docker-compose logs -f service-client

# Reconstruire les images
docker-compose up -d --build

# Nettoyer (supprimer volumes et images)
docker-compose down -v --rmi all
```

## 5. En cas de problème
```bash
# Vérifier les ports utilisés
netstat -an | findstr :3001
netstat -an | findstr :8074
netstat -an | findstr :8075
netstat -an | findstr :8076

# Redémarrer Docker Desktop si nécessaire
```

## 6. Tests rapides
```bash
# Test API Client
curl http://localhost:8074/api/clients

# Test API Produit
curl http://localhost:8075/api/produits

# Test API Commande
curl http://localhost:8076/api/commandes
```
