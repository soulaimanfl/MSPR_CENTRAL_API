# MSPR Microservices – Journal des actions

Date: 2025-09-15

## Résumé haut-niveau
- Mise en place d’un service Client complet (JWT, RabbitMQ, MySQL, tests, monitoring) et correction des points bloquants.
- Ajout d’un repo central orchestrateur (monorepo avec sous-modules) pour Client, Produit, Commande, Frontend.
- CI/CD unifié (GitHub Actions) avec build conditionnel par service et push d’images vers GHCR.
- Orchestration Docker Compose complète (3 APIs, 3 MySQL, RabbitMQ, PhpMyAdmin, Prometheus, Grafana, Alertmanager, Frontend).
- Ajout d’un contexte panier côté Frontend et intégration du compteur sur la Navbar.

## Détails par étape

### 1) Audit du repo service_client
- Analyse de la structure, CI/CD, Docker, Sonar, monitoring.
- Constat: JWT présent mais `JwtUtil` vide, RabbitMQ configuré, tests présents, monitoring partiel.

### 2) Sécurité JWT
- Implémentation complète de `src/util/JwtUtil.java` (génération/validation, claims, rôles, expiration).
- Vérification `AuthController`, `JwtAuthFilter`, `SecurityConfig`.

### 3) RabbitMQ & Messaging
- Validation `RabbitConfigClient` (exchange/queues/bindings), `ClientPublisher`, `ClientConsumers`.

### 4) Monitoring
- Complétion des configs Prometheus/Grafana/Alertmanager dans `ops/`.
- Exposition Actuator Prometheus sur 8081.

### 5) Docker Compose (service_client)
- Ajout du volume `rabbitmq_data` pour corriger `.erlang.cookie` permissions.
- Correction MySQL init script: création table `client` avant seed.
- Reconstruction locale de l’image au lieu d’utiliser GHCR pour intégrer nos changements.

### 6) Tests de bout en bout
- Démarrage infra (MySQL, RabbitMQ, API, monitoring).
- Test login JWT via PowerShell `Invoke-RestMethod`.

### 7) Repo central orchestrateur
- Création `mspr-microservices/` avec sous-modules:
  - `services/service-client` (kuetcheal/service_client)
  - `services/service-produit` (kuetcheal/service_produit)
  - `services/service-commande` (kuetcheal/service_commande)
  - `frontend` (kuetcheal/webshop_mspr)
- CI/CD unifié `.github/workflows/ci.yml`:
  - Détection de changements par service
  - Build/Test Maven/Node par service
  - Build/Push Docker vers GHCR
- `docker-compose.yml` central:
  - 3 MySQL (3307/3308/3309), RabbitMQ, PhpMyAdmin
  - services: client(8074), produit(8075), commande(8076)
  - monitoring: Prometheus(9090), Grafana(3000), Alertmanager(9093)
  - Frontend Nginx(3000) + proxy vers APIs

### 8) Frontend (webshop_mspr)
- Ajout sous-module `frontend/`.
- Dockerfile multi-stage (Node build + Nginx)
- Nginx conf (SPA + proxy /api/* vers services)
- Contexte React du panier `src/contexts/CartContext.jsx`
- Comptage dynamique dans la Navbar (`Badge` MUI)
- Enrobage App avec `CartProvider`

## Commandes utiles
- Démarrer stack centrale: `docker-compose up -d`
- Voir statut: `docker-compose ps`
- Mettre à jour sous-modules: `git submodule update --remote`

## Prochaines étapes
- Implémenter intégralement services Produit/Commande (domaines + endpoints + events).
- Brancher le frontend aux vraies APIs (produits, panier, commandes).
- Ajouter tests d’intégration inter-services (Testcontainers + RabbitMQ).
