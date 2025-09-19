# Guide de Test - MSPR Microservices

## 🧪 Tests Automatisés

### CI/CD Pipeline
- **Fichier** : `.github/workflows/ci-cd.yml`
- **Déclenchement** : Push sur `master`
- **Services testés** : Client, Produit, Commande, Frontend

### Tests Backend
```bash
# Tests unitaires Java
mvn test

# Tests d'intégration
mvn verify

# Tests avec Docker
docker-compose up -d
docker-compose exec service-client mvn test
```

### Tests Frontend
```bash
# Tests unitaires React
npm test

# Tests E2E (si configurés)
npm run test:e2e
```

## 🔍 Tests Manuels

### 1. Test des APIs

#### Service Client (Port 8074)
```bash
# Test de l'authentification
curl -X POST http://localhost:8074/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin"}'

# Test de la création de client
curl -X POST http://localhost:8074/api/clients \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{"name":"Test Client","email":"test@example.com"}'
```

#### Service Produit (Port 8075)
```bash
# Test de récupération des produits
curl -X GET http://localhost:8075/api/produits \
  -H "Authorization: Bearer <token>"

# Test de création de produit
curl -X POST http://localhost:8075/api/produits \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{"name":"Test Product","price":99.99,"stock":10}'
```

#### Service Commande (Port 8076)
```bash
# Test de récupération des commandes
curl -X GET http://localhost:8076/api/commandes \
  -H "Authorization: Bearer <token>"

# Test de création de commande
curl -X POST http://localhost:8076/api/commandes \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{"clientId":1,"produits":[{"id":1,"quantity":2}]}'
```

### 2. Test du Frontend

#### Authentification
1. **Accéder à** : http://localhost:3001/products
2. **Vérifier** : Message "Accès requis"
3. **Cliquer** : "Se connecter"
4. **Saisir** : admin / admin
5. **Vérifier** : Redirection vers les produits

#### Catalogue Produits
1. **Vérifier** : Affichage des 10 produits de test
2. **Vérifier** : Images s'affichent correctement
3. **Tester** : Ajout au panier
4. **Vérifier** : Gestion du stock

#### Panier
1. **Ajouter** : Plusieurs produits
2. **Vérifier** : Calcul du total
3. **Tester** : Suppression d'articles
4. **Vérifier** : Validation de commande

### 3. Test du Monitoring

#### Prometheus
1. **Accéder à** : http://localhost:9090
2. **Vérifier** : Targets "UP"
3. **Tester** : Requêtes PromQL
   - `up` : Statut des services
   - `jvm_memory_used_bytes` : Mémoire JVM
   - `http_server_requests_seconds_count` : Requêtes HTTP

#### Grafana
1. **Accéder à** : http://localhost:3000
2. **Connexion** : admin / admin
3. **Vérifier** : Dashboards chargés
4. **Tester** : Requêtes de métriques

## 📊 Données de Test

### Produits de Test
Le système inclut 10 produits de test avec images :
- iPhone 15 Pro (1199.99€)
- Samsung Galaxy S24 (999.99€)
- MacBook Pro M3 (1999.99€)
- Dell XPS 13 (1299.99€)
- AirPods Pro (249.99€)
- Sony WH-1000XM5 (399.99€)
- Apple Watch Series 9 (399.99€)
- iPad Air (599.99€)
- Nintendo Switch (299.99€)
- PlayStation 5 (499.99€)

### Utilisateur de Test
- **Username** : admin
- **Password** : admin
- **Rôle** : ADMIN

## 🐛 Dépannage des Tests

### Problèmes courants

#### 1. Services non démarrés
```bash
# Vérifier le statut
docker-compose ps

# Redémarrer un service
docker-compose restart service-client
```

#### 2. Erreurs de base de données
```bash
# Vérifier les logs
docker logs mysql_client
docker logs mysql_produit
docker logs mysql_commande

# Redémarrer les bases
docker-compose restart mysql_client mysql_produit mysql_commande
```

#### 3. Erreurs CORS
- **Vérifier** : Configuration dans SecurityConfig.java
- **Ports autorisés** : 3001, 5173
- **Headers** : Authorization, Content-Type

#### 4. Images ne s'affichent pas
- **Vérifier** : URLs dans la base de données
- **Tester** : Accès direct aux URLs
- **Fallback** : Message "Pas d'image"

## 📈 Métriques de Performance

### Temps de réponse
- **API Client** : < 200ms
- **API Produit** : < 150ms
- **API Commande** : < 300ms
- **Frontend** : < 2s

### Utilisation mémoire
- **Services Java** : < 512MB
- **Frontend** : < 100MB
- **Bases de données** : < 256MB

### Disponibilité
- **Uptime** : > 99.9%
- **MTTR** : < 5 minutes
- **RTO** : < 1 heure

## 🔄 Tests de Charge

### Scénarios
1. **Connexions simultanées** : 100 utilisateurs
2. **Requêtes API** : 1000 req/min
3. **Upload d'images** : 10MB max
4. **Sessions** : 1 heure max

### Outils recommandés
- **JMeter** : Tests de charge
- **Postman** : Tests d'API
- **Selenium** : Tests E2E
- **Lighthouse** : Performance frontend
