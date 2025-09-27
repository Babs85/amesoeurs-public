<img width="1440" height="900" alt="Capture d’écran 2025-09-27 à 18 01 25" src="https://github.com/user-attachments/assets/760440ed-aa2d-4e84-b674-2916c701c57e" />
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

## 👤 Auteur

**Ababacar Niang**
Développeur Java – Passionné par Spring Boot & les applications back-end.
📧 [ababacar.sedikh@hotmail.fr](mailto:ababacar.sedikh@hotmail.fr)

---
