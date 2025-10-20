<img width="576" height="167" alt="7" src="https://github.com/user-attachments/assets/150b13e2-50ac-4938-a7e6-0d54c42077aa" />
<img width="583" height="339" alt="6" src="https://github.com/user-attachments/assets/0c05c156-81cb-4e46-bb91-11aa9530fd95" />
<img width="583" height="339" alt="5" src="https://github.com/user-attachments/assets/e71f1e9a-cd69-4498-9da9-032811f81a8f" />
<img width="583" height="339" alt="4" src="https://github.com/user-attachments/assets/e1a6eb81-238e-4980-8a0c-71dc796adff6" />
<img width="583" height="339" alt="3" src="https://github.com/user-attachments/assets/7c01c01f-794d-4263-b7d7-cf37d176527f" />
<img width="608" height="258" alt="2" src="https://github.com/user-attachments/assets/efdf7390-b95a-490a-a1de-dc20c89a7592" />
<img width="593" height="430" alt="1" src="https://github.com/user-attachments/assets/5505f6bb-14d2-4363-bd4f-1bc350328586" />
### 📄 `README.md`

```markdown
# TP2 - Docker et Docker Compose (MERN Stack)

## 🎯 Objectif du TP
Mettre en place une application **MERN** (MongoDB, Express, React, Node.js) entièrement conteneurisée à l’aide de **Docker** et **Docker Compose**.

L’application comprend :
- Un **serveur Node.js / Express** connecté à MongoDB.
- Une **interface React** pour interagir avec les données.
- Une base de données **MongoDB**.

---

## 🧱 Structure du projet

```

mern-app/
│
├── client/              # Application React (frontend)
│   ├── Dockerfile
│   └── src/
│
├── server/              # API Express (backend)
│   ├── Dockerfile
│   ├── server.js
│   ├── routes/record.js
│   └── db/conn.js
│
├── docker-compose.yml   # Configuration Docker Compose
└── README.md

````

---

## 🐳 Étapes réalisées

### 1️⃣ Création des Dockerfiles

#### **Dockerfile du serveur (Express)**

Fichier : `server/Dockerfile`

```dockerfile
FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 9000
ENV MONGO_URI="mongodb://mongodb:27017/mern"
CMD ["npm", "start"]
````

👉 Ce Dockerfile :

* Utilise `node:lts-alpine` comme image de base.
* Installe les dépendances.
* Copie le code du serveur.
* Expose le port **9000**.
* Définit la variable d’environnement `MONGO_URI`.
* Lance l’application avec `npm start`.

---

#### **Dockerfile du client (React)**

Fichier : `client/Dockerfile`

```dockerfile
FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
RUN npm install -g serve
EXPOSE 3000
CMD ["serve", "-s", "build", "-l", "3000"]
```

👉 Ce Dockerfile :

* Construit l’application React avec `npm run build`.
* Installe `serve` pour héberger la version de production.
* Expose le port **3000** pour l’accès web.

---

### 2️⃣ Création du fichier Docker Compose

Fichier : `docker-compose.yml`

```yaml
version: "3.8"

services:
  # MongoDB
  mongodb:
    image: mongo:latest
    container_name: mongodb-mern
    networks:
      - mern-network

  # Serveur Express
  server:
    build:
      context: ./server
      dockerfile: Dockerfile
    container_name: server
    ports:
      - "9000:9000"
    depends_on:
      - mongodb
    environment:
      - MONGO_URI=mongodb://mongodb:27017/mern
    networks:
      - mern-network

  # Client React
  client:
    build:
      context: ./client
      dockerfile: Dockerfile
    container_name: client
    ports:
      - "3000:3000"
    depends_on:
      - server
    networks:
      - mern-network

networks:
  mern-network:
    driver: bridge
```

👉 Ce fichier :

* Définit trois services : **mongodb**, **server**, **client**.
* Connecte les trois services sur un même réseau `mern-network`.
* Lie les ports :

  * `3000` → client React
  * `9000` → serveur Express
* Gère les dépendances (`depends_on`).

---

### 3️⃣ Lancement de l’application

#### **Étape 1 : Construction et exécution**

Depuis le répertoire racine du projet :

```bash
sudo docker compose up --build
```

#### **Étape 2 : Accès aux services**

* **Frontend (React)** → [http://localhost:3000](http://localhost:3000)
* **Backend (Express API)** → [http://localhost:9000](http://localhost:9000)

---

### 4️⃣ Tests et vérifications

#### Vérifier les conteneurs :

```bash
sudo docker ps
```

#### Consulter les logs :

```bash
sudo docker compose logs -f server
sudo docker compose logs -f client
sudo docker compose logs -f mongodb
```

#### Tester la connexion à l’API :

```bash
curl http://localhost:9000/
```

Résultat attendu :

```
App is running
```

---

### 5️⃣ Arrêt et nettoyage

Pour arrêter et supprimer tous les conteneurs :

```bash
sudo docker compose down
```

---

## 🧠 Conclusion

Ce TP met en œuvre la **conteneurisation complète d’une application MERN**.
Chaque composant (MongoDB, backend, frontend) est isolé dans son propre conteneur, mais tous communiquent via le réseau Docker `mern-network`.
Grâce à Docker Compose, un seul fichier permet de **lancer, arrêter et reconstruire** l’ensemble de l’application en une commande.

---

## 👩‍💻 Auteure

**Sarah Ghabri**
Étudiante en Génie Logiciel
TP2 - Docker & Docker Compose

```

