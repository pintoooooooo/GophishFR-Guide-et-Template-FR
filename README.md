# GophishFR — Guide d'installation sous Windows avec Docker

> Outil de sensibilisation au phishing à usage interne, avec consentement des participants.

---

## Prérequis

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installé et démarré
- Git

---

## Installation

### 1. Cloner le dépôt

```bash
git clone https://github.com/votre-compte/gophishfr-guide.git
cd gophishfr-guide
```

### 2. Préparer les fichiers de config

```bash
cp config.example.json config.json
```

Ouvrir `config.json` et remplacer `VOTRE_USER` et `VOTRE_MOT_DE_PASSE` par les valeurs de votre choix (les mêmes que dans `docker-compose.yml`).

### 3. Générer les certificats SSL

Dans le dossier `certs/`, ouvrir un terminal et exécuter :

```bash
openssl req -x509 -newkey rsa:4096 -keyout gophish_admin.key \
  -out gophish_admin.crt -days 365 -nodes \
  -subj "/CN=gophish-admin"
```

> Si `openssl` n'est pas disponible sur Windows, utiliser [Git Bash](https://gitforwindows.org/) ou [WSL](https://learn.microsoft.com/fr-fr/windows/wsl/).

### 4. Ouvrir le port 3333 dans le pare-feu Windows

1. Démarrer → **Pare-feu Windows avec fonctions avancées de sécurité**
2. **Règles d'entrée** → Nouvelle règle
3. **Port** → TCP → `3333`
4. **Autoriser la connexion** → Terminer

### 5. Lancer GophishFR

```bash
docker-compose up -d
```

Vérifier que tout tourne :

```bash
docker ps
```

---

## Accès à l'interface

Ouvrir dans le navigateur :

```
https://IP_DU_SERVEUR:3333
```

Identifiants par défaut :

```
Utilisateur : admin
Mot de passe : (voir les logs)
```

```bash
docker logs gophishfr | grep "Please login with the username admin"
```

---

## Mise à jour

```bash
docker-compose down
docker pull gophishfr/gophishfr:latest
docker-compose up -d
```

---

## Templates inclus

### Emails (`templates/emails/`)

| Fichier | Scénario |
|---|---|
| `mot-de-passe-expire.html` | Alerte expiration de compte |
| `colis-en-attente.html` | Faux avis de livraison |

### Pages de destination (`templates/landing-pages/`)

| Fichier | Description |
|---|---|
| `portail-connexion.html` | Fausse page de login (capture de credentials) |
| `sensibilisation.html` | Page révélant l'exercice après le clic ✅ |

> **Recommandation** : configurer GophishFR pour rediriger vers `sensibilisation.html` après soumission du formulaire.

---

## Structure du dépôt

```
├── .gitignore
├── config.example.json   ← modèle (config.json réel est ignoré par git)
├── docker-compose.yml
├── certs/                ← vos certificats SSL (ignorés par git)
└── templates/
    ├── emails/
    └── landing-pages/
```
