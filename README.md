💬 Nest GraphQL Chat Backend

Projet M1 – EFREI : micro‑service de messagerie en temps réel basé sur NestJS, GraphQL, RabbitMQ et MongoDB.

🗂️ Tech stack actuelle

Couche

Outil

Rôle

API HTTP

NestJS 11 + @nestjs/graphql

schéma GraphQL, mutations / queries / subscriptions

Broker

RabbitMQ 3

file chat_queue pour les messages

Persistence

MongoDB 7 + Mongoose 8

collections users, messages, conversations

Temps réel

graphql‑subscriptions / PubSub

pousse messageAdded aux clients WebSocket

Containers

Docker / docker‑compose

RabbitMQ + Mongo en local

🚀 Mise en route (DEV)

# 1. cloner et installer
npm install

# 2. lancer les services externes
docker compose up -d rabbitmq mongo

# 3. compiler & démarrer lʼAPI
npm run build && npm start        # http://localhost:3000/graphql

# 4. démarrer le worker RabbitMQ → Mongo
npm run start:worker              # écoute chat_queue

Variables dʼenvironnement

Créer .env à la racine :

MONGO_URI=mongodb://localhost:27017/chatdb

📝 Exemples de requêtes (Postman ou GraphQL Playground)

Créer un utilisateur

mutation {
  createUser(username: "Alice") {
    id username createdAt
  }
}

Envoyer un message (optimiste)

mutation Send($cid: ID!, $content: String!) {
  sendMessage(conversationId: $cid, content: $content) {
    id content author { username } createdAt
  }
}

Variables :

{ "cid": "64b9454c1d7f9320fcbe196a", "content": "Hello Mongo!" }

Sʼabonner à la conversation

subscription OnMsg($cid: ID!) {
  messageAdded(conversationId: $cid) {
    id content author { username }
  }
}

📁 Structure des dossiers (backend)

src/
├── schemas/            # Mongoose + GraphQL (User, Message, Conversation)
├── chat.resolver.ts    # queries + createUser
├── message.resolver.ts # sendMessage + subscription
├── message.service.ts  # persistance côté worker
├── rabbitmq/           # module + service client
├── worker.ts           # bootstrap micro‑service RMQ
└── ...

🐇 RabbitMQ Dashboard

URL : http://localhost:15672

Auth : guest**guest**

Queue chat_queue : voir les messages empilés quand le worker est off.

🍃 MongoDB quick CLI

mongosh "mongodb://localhost:27017/chatdb" --eval "db.users.find().pretty()"

⚙️ Scripts npm utiles

Commande

Description

npm start

lance /dist/main.js (API)

npm run start:dev

API en hot‑reload (ts‑node‑dev)

npm run start:worker

lance /dist/worker.js

npm run build

compile TypeScript → dist/
