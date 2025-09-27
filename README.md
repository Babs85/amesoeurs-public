
# 🌐 AmeSoeurs – Application de rencontre qui se base sur des critères scientifiques pour créer des matchs, conçu avec Spring Boot 3.1.0

## 📌 Description

AmeSoeurs est une application web de rencontre développée avec **Spring Boot 3.1**.
Elle permet aux utilisateurs de s’inscrire, de definir un profil amoureu á partir d'un questionnaire, de gérer leur profil et d’échanger via un système de chat en temps réel basé sur **WebSockets**.

Le projet a été conçu comme un **projet personnel d’apprentissage avancé de Spring Boot**, avec des fonctionnalités modernes (sécurité, paiements en ligne, authentification JWT, etc.).

---

## ⚙️ Fonctionnalités principales

* 🔐 **Authentification et sécurité**

  * Authentification JWT
  * Spring Security (gestion des rôles & permissions)
  * OAuth2 Client
* 👤 **Gestion utilisateurs**

  * Création et gestion des profils
  * Stockage en base MySQL
* 💬 **Chat en temps réel**

  * WebSocket + STOMP
  * Liste des utilisateurs connectés
* 💳 **Paiements & abonnements**

  * Intégration **Stripe API**
* 📧 **Communication**

  * Envoi automatique d’e-mails avec `spring-boot-starter-mail`
* 🗄️ **Base de données**

  * Spring Data JPA / Hibernate
  * MySQL Connector
* 🚀 **Déploiement**

  * Packaging en **WAR** (déploiement Tomcat possible)
  * Support DevTools pour le rechargement à chaud

---

## 🛠️ Technologies utilisées

* **Langage :** Java 19
* **Framework :** Spring Boot 3.1
* **Modules Spring :** Security, Data JPA, Web, WebSocket, Mail, OAuth2
* **BDD :** MySQL
* **Messaging :** STOMP / WebSocket
* **Paiements :** Stripe Java SDK
* **Build tool :** Maven
* **Autres :** Lombok, JSON, Jenkins/CI

---

## 📂 Structure du projet

```
src/main/java/com/amesoeurs
    ├── config        # Configuration Spring Security, WebSocket, etc.
    ├── controller    # API REST & endpoints WebSocket
    ├── entity        # Entités JPA
    ├── repository    # DAO JPA
    ├── service       # Logique métier
    └── Application.java   # Classe main Spring Boot
```

---

## 📦 Dépendances Maven (principales)

Extrait du `pom.xml` :

* `spring-boot-starter-web`
* `spring-boot-starter-data-jpa`
* `spring-boot-starter-security`
* `spring-boot-starter-websocket`
* `spring-boot-starter-mail`
* `spring-boot-starter-oauth2-client`
* `mysql-connector-java`
* `jjwt (api, impl, jackson)`
* `stripe-java`
* `lombok`


---

## 🚀 Installation & exécution

1. **Cloner le projet**

   ```bash
   git clone https://github.com/Babs85/amesoeurs.git
   cd amesoeurs
   ```
2. **Configurer la base MySQL**

   * Créer une base `amesoeurs_db`
   * Modifier `src/main/resources/application.properties` :

     ```properties
     spring.datasource.url=jdbc:mysql://localhost:3306/amesoeurs_db
     spring.datasource.username=ton_user
     spring.datasource.password=ton_mot_de_passe
     spring.jpa.hibernate.ddl-auto=update
     ```
3. **Lancer l’application**

   ```bash
   mvn spring-boot:run
   ```
4. **Accéder à l’application** (en local)

   * API REST : `http://localhost:8080`
   * Interface chat : `http://localhost:8080/chat`

---

## 📸 Captures d’écran (à ajouter)
<img width="1440" height="900" alt="Capture d’écran 2025-09-27 à 18 01 25" src="https://github.com/user-attachments/assets/f9086243-5c1e-4b4c-acdc-dcc53f944bc9" />

<img width="1440" height="900" alt="Capture d’écran 2025-09-27 à 18 01 32" src="https://github.com/user-attachments/assets/800ef133-50a4-47ce-acd9-830388df7fb3" />

*----*

---
Fonctionnement chat messages avec websocket
---

## 🟢 1. Configuration WebSocket (`WebSocketConfig`)

* La classe est annotée avec `@EnableWebSocketMessageBroker` → ça active **STOMP** sur WebSocket.

* **`configureMessageBroker`** :

  * `enableSimpleBroker("/user")` → le **broker interne** Spring gère les destinations `/user/...` (messages envoyés aux clients).
  * `setApplicationDestinationPrefixes("/app")` → les messages envoyés à `/app/...` vont dans le **backend** (contrôleurs STOMP).
  * `setUserDestinationPrefix("/user")` → permet d’envoyer des messages ciblés (par exemple `/user/john/queue/messages`).

* **`registerStompEndpoints`** :

  * Expose `/ws` comme point d’entrée WebSocket.
  * `.withSockJS()` → fallback si le navigateur ne supporte pas WebSocket natif.

* **`configureMessageConverters`** :

  * Force le format JSON par défaut avec `MappingJackson2MessageConverter`.
  * Chaque message envoyé/reçu passe en JSON via Jackson.

👉 Résumé : ici tu définis le **canal de communication** et les **règles de routage** des messages.

---

## 🟢 2. Le contrôleur `ChatMessageController`

C’est lui qui **reçoit, traite et renvoie** les messages.

### 🔹 a) `@MessageMapping("/chat") → processMessage`

* Chaque message envoyé par le client à `/app/chat` arrive ici.
* Workflow :

  1. **Différencie un message privé (PRIVATE) ou de groupe (TRIPLE, DOUBLE_CHAPERON)**.
  2. **PRIVATE** :

     * Normalise le `chatId` → toujours hommeId_femmeId.
     * Sauvegarde le message.
     * Crée une **chatroom** si elle n’existe pas encore.
     * Crée une **notification** (table `ChatNotification`).
     * Envoie le message :

       * au destinataire (`convertAndSendToUser` → `/user/{recipient}/queue/messages`).
       * et aux chaperons liés (destinataire et expéditeur).
  3. **Groupes (TRIPLE/DOUBLE_CHAPERON)** :

     * Sauvegarde en base.
     * Broadcast au topic `/topic/group/{chatId}` (tous les membres abonnés reçoivent).

👉 Ici, tu gères à la fois la **persistance** (BD), les **notifications** et la **diffusion temps réel**.

---

### 🔹 b) Récupération de messages

* `@GetMapping("/messages/{sender}/{recipient}")` → récupère l’historique des messages privés.
* `@GetMapping("/group/{chatId}/messages")` → récupère l’historique d’un groupe.

👉 Sert au front pour afficher l’historique au moment où tu ouvres une discussion.

---

### 🔹 c) Récupération des discussions (liste des chats)

* `@GetMapping("/user/{pseudo}/chats")` → liste tous les chats/matchs d’un utilisateur.

  * Récupère les **chatrooms existants**.
  * Ajoute les **utilisateurs matchés sans chatroom** (fakeChat).
  * Détermine si le chat est **PRIVATE**, **TRIPLE** ou **DOUBLE_CHAPERON** en fonction de la présence de chaperons.
  * Trie les discussions.

👉 C’est ce qui alimente la **liste de discussions** côté front.

---

### 🔹 d) Gestion des chaperons

* `handleChaperonNotifications` → si un message a des chaperons liés (`relatedChaperons`), le contrôleur envoie une copie du message dans leur `queue/messages`.
* `determineUserType` → identifie si l’utilisateur est **CHAPERON**, **FILLEUL**, ou **NORMAL**.

👉 Cette logique permet d’inclure automatiquement les chaperons dans les échanges.

---

## 🟢 3. Le cycle complet d’un message

1. Le client envoie un message à `/app/chat` via STOMP.
2. Le serveur reçoit → `processMessage`.
3. Le message est **sauvegardé en base** (`ChatMessageService`).
4. Une **notification** est créée (`ChatNotificationRepo`).
5. Le serveur envoie le message :

   * au destinataire (`/user/{recipient}/queue/messages`),
   * aux chaperons (`/user/{chaperon}/queue/messages`),
   * ou au groupe (`/topic/group/{chatId}`).
6. Le client abonné reçoit le message en temps réel.

---

## 🟢 4. En résumé synthétique

* **`WebSocketConfig`** → configure STOMP + broker interne.
* **`ChatMessageController`** → cœur de la logique métier :

  * Sauvegarde en base (messages + notifications).
  * Diffusion en temps réel (destinataires, chaperons, groupes).
  * Gestion des discussions (liste de chats, types, matches).
* **Flux côté utilisateur** → REST (pour historique et liste) + WebSocket (pour temps réel).

👉 En clair : le système combine **WebSocket temps réel** avec **REST classique** pour fournir un chat persistant, sécurisé et adapté aux cas avec **chaperons**.









## 👤 Auteur

**Ababacar Niang**
Développeur Java – Passionné par Spring Boot & les applications back-end.
📧 [ababacar.sedikh@hotmail.fr](mailto:ababacar.sedikh@hotmail.fr)

---
