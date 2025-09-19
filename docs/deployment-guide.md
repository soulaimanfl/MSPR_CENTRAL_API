# Guide de Déploiement - MSPR Microservices

## 🚀 Déploiement Local

### Prérequis
- Docker Desktop 4.0+
- Docker Compose 2.0+
- Git
- 8GB RAM minimum
- 10GB espace disque

### Installation
```bash
# Cloner le repository
git clone --recursive https://github.com/kuetcheal/mspr-microservices.git
cd mspr-microservices

# Démarrer tous les services
docker-compose up -d

# Vérifier le statut
docker-compose ps
```

### Services démarrés
- **Frontend** : http://localhost:3001
- **API Client** : http://localhost:8074
- **API Produit** : http://localhost:8075
- **API Commande** : http://localhost:8076
- **Grafana** : http://localhost:3000
- **Prometheus** : http://localhost:9090
- **phpMyAdmin** : http://localhost:8085

## 🌐 Déploiement Production

### Architecture Recommandée

#### Environnement de Production
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Load Balancer │    │   Load Balancer │    │   Load Balancer │
│   (Nginx/HAProxy)│    │   (Nginx/HAProxy)│    │   (Nginx/HAProxy)│
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │   API Gateway   │    │   Monitoring    │
│   (React)       │    │   (Spring Cloud)│    │   (Grafana)     │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Microservices │    │   Microservices │    │   Microservices │
│   (Docker Swarm)│    │   (Kubernetes)  │    │   (Docker)      │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Configuration Docker Swarm

#### 1. Initialiser le Swarm
```bash
# Sur le manager
docker swarm init

# Sur les workers
docker swarm join --token <token> <manager-ip>:2377
```

#### 2. Déployer les services
```bash
# Créer le stack
docker stack deploy -c docker-compose.prod.yml mspr-stack

# Vérifier le déploiement
docker stack services mspr-stack
```

#### 3. Configuration de production
```yaml
# docker-compose.prod.yml
version: '3.8'
services:
  frontend:
    image: mspr-microservices-frontend:latest
    ports:
      - "80:3000"
    environment:
      - NODE_ENV=production
    deploy:
      replicas: 3
      resources:
        limits:
          memory: 512M
        reservations:
          memory: 256M
```

### Configuration Kubernetes

#### 1. Namespace
```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mspr-microservices
```

#### 2. ConfigMap
```yaml
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mspr-config
  namespace: mspr-microservices
data:
  MYSQL_ROOT_PASSWORD: "secure-password"
  JWT_SECRET: "production-jwt-secret"
```

#### 3. Services
```yaml
# services.yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
  namespace: mspr-microservices
spec:
  selector:
    app: frontend
  ports:
    - port: 80
      targetPort: 3000
  type: LoadBalancer
```

## 🔧 Configuration Environnement

### Variables d'environnement

#### Frontend
```bash
VITE_API_BASE_URL_CLIENT=https://api.example.com/client
VITE_API_BASE_URL_PRODUIT=https://api.example.com/produit
VITE_API_BASE_URL_COMMANDE=https://api.example.com/commande
```

#### Backend
```bash
# Base de données
SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/service_client
SPRING_DATASOURCE_USERNAME=root
SPRING_DATASOURCE_PASSWORD=secure-password

# JWT
JWT_SECRET=production-jwt-secret-key
JWT_EXPIRATION=3600000

# RabbitMQ
SPRING_RABBITMQ_HOST=rabbitmq
SPRING_RABBITMQ_PORT=5672
SPRING_RABBITMQ_USERNAME=guest
SPRING_RABBITMQ_PASSWORD=guest
```

### Sécurité

#### 1. HTTPS/TLS
```nginx
# nginx.conf
server {
    listen 443 ssl;
    server_name api.example.com;
    
    ssl_certificate /etc/ssl/certs/api.crt;
    ssl_certificate_key /etc/ssl/private/api.key;
    
    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

#### 2. Firewall
```bash
# UFW (Ubuntu)
ufw allow 22/tcp    # SSH
ufw allow 80/tcp    # HTTP
ufw allow 443/tcp   # HTTPS
ufw enable
```

#### 3. Secrets Management
```bash
# Docker Secrets
echo "secure-password" | docker secret create mysql_password -
echo "jwt-secret" | docker secret create jwt_secret -
```

## 📊 Monitoring Production

### Prometheus Configuration
```yaml
# prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - "alert_rules.yml"

scrape_configs:
  - job_name: 'mspr-services'
    static_configs:
      - targets: ['service-client:8081', 'service-produit:8082', 'service-commande:8083']
```

### Grafana Dashboards
- **Application Metrics** : JVM, HTTP requests, response times
- **Infrastructure Metrics** : CPU, Memory, Disk, Network
- **Business Metrics** : Orders, Products, Users

### Alerting Rules
```yaml
# alert_rules.yml
groups:
  - name: mspr-alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
```

## 🔄 CI/CD Production

### GitHub Actions Workflow
```yaml
# .github/workflows/deploy-prod.yml
name: Deploy Production
on:
  push:
    branches: [main]
    tags: ['v*']

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Production
        run: |
          docker-compose -f docker-compose.prod.yml up -d
```

### Blue-Green Deployment
```bash
# Script de déploiement
#!/bin/bash
# 1. Déployer la nouvelle version (green)
docker-compose -f docker-compose.green.yml up -d

# 2. Tester la nouvelle version
curl -f http://green.example.com/health || exit 1

# 3. Basculer le trafic
nginx -s reload

# 4. Arrêter l'ancienne version (blue)
docker-compose -f docker-compose.blue.yml down
```

## 🚨 Dépannage Production

### Logs
```bash
# Logs des services
docker-compose logs -f service-client
docker-compose logs -f frontend

# Logs Kubernetes
kubectl logs -f deployment/frontend -n mspr-microservices
```

### Health Checks
```bash
# Vérifier la santé des services
curl http://localhost:8074/actuator/health
curl http://localhost:8075/actuator/health
curl http://localhost:8076/actuator/health
```

### Backup
```bash
# Backup des bases de données
docker exec mysql_client mysqldump -u root -p service_client > backup_client.sql
docker exec mysql_produit mysqldump -u root -p service_produit > backup_produit.sql
docker exec mysql_commande mysqldump -u root -p service_commande > backup_commande.sql
```

## 📈 Performance

### Optimisations
- **Caching** : Redis pour les sessions
- **CDN** : CloudFlare pour les assets statiques
- **Load Balancing** : Nginx/HAProxy
- **Database** : Indexes optimisés, connection pooling

### Métriques
- **Response Time** : < 200ms (95th percentile)
- **Throughput** : > 1000 req/s
- **Availability** : > 99.9%
- **Error Rate** : < 0.1%
