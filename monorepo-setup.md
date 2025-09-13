# Configuration Monorepo avec sous-modules Git

## 1. Créer le monorepo principal
```bash
git init mspr-microservices
cd mspr-microservices
```

## 2. Ajouter les sous-modules
```bash
# Ajouter service_client
git submodule add https://github.com/kuetcheal/service_client.git services/service-client

# Ajouter service_produit  
git submodule add https://github.com/kuetcheal/service_produit.git services/service-produit

# Ajouter service_commande
git submodule add https://github.com/kuetcheal/service_commande.git services/service-commande
```

## 3. Structure finale
```
mspr-microservices/
├── .github/workflows/
│   ├── ci.yml
│   └── deploy.yml
├── services/
│   ├── service-client/     (sous-module)
│   ├── service-produit/    (sous-module)
│   └── service-commande/   (sous-module)
├── docker-compose.yml
└── README.md
```

## 4. Pipeline CI/CD unifié
Le pipeline détecte automatiquement les changements dans chaque service et ne build que les services modifiés.
