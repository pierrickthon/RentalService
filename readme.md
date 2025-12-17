# RentalService – Build, Exécution locale et Dockerisation

## 🎯 Objectif du TP

Ce projet a pour but de :

1. **Tester une application Java sans Docker**
2. **Builder et exécuter l’application en local**
3. **Créer une image Docker**
4. **Lancer l’application via Docker**

L’application expose une route HTTP simple :

```
GET /bonjour
```

accessible sur le port **8080**.

---

## 🧰 Prérequis

* Java **JDK 21**
* Docker
* Un terminal (macOS, Linux ou Windows)

---

# 🟦 PARTIE 1 – Tester le programme **sans Docker**

## 1️⃣ Installation et vérification de Java

Vérifier que Java 21 est installé :

```bash
java -version
```

Résultat attendu :

```
java version "21"
```

---

## 2️⃣ Build du projet avec Gradle

Se placer à la racine du projet :

```bash
cd RentalService
```

Lancer la compilation :

```bash
./gradlew build
```

Cette commande :

* Télécharge Gradle
* Compile le code Java
* Génère un fichier JAR exécutable

Résultat attendu :

```
BUILD SUCCESSFUL
```

📸 Illustration :

![Gradle build successful](Capture d’écran 2025-12-17 à 16.07.01.png)

---

## 3️⃣ Lancer l’application en local

Exécuter le fichier JAR généré :

```bash
java -jar build/libs/RentalService-0.0.1-SNAPSHOT.jar
```

L’application démarre un serveur web sur le port **8080**.

---

## 4️⃣ Vérification dans le navigateur

Ouvrir l’URL suivante :

```
http://localhost:8080/bonjour
```

Résultat attendu :

```
bonjour
```

📸 Illustration :

![Endpoint bonjour](Capture d’écran 2025-12-17 à 16.15.34.png)

👉 Cette étape confirme que l’application fonctionne **sans Docker**.

---

# 🟦 PARTIE 2 – Dockerisation de l’application

## 5️⃣ Création du Dockerfile

Créer un fichier `Dockerfile` à la racine du projet.

```dockerfile
FROM eclipse-temurin:21-jre-jammy

VOLUME /tmp

EXPOSE 8080

ADD ./build/libs/RentalService-0.0.1-SNAPSHOT.jar app.jar

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

### Explication :

* **FROM** : image Java 21 (JRE uniquement, plus légère)
* **VOLUME /tmp** : gestion des fichiers temporaires
* **EXPOSE 8080** : port utilisé par l’application
* **ADD** : copie du JAR dans l’image Docker
* **ENTRYPOINT** : commande lancée au démarrage du conteneur

---

## 6️⃣ Build de l’image Docker

À la racine du projet :

```bash
docker build -t rentalservice .
```

Résultat attendu :

* Téléchargement de l’image Java
* Création de l’image Docker `rentalservice`

📸 Illustration :

![Docker build](Capture d’écran 2025-12-17 à 16.30.12.png)

---

## 7️⃣ Lancer le conteneur Docker

```bash
docker run -p 8080:8080 rentalservice
```

Puis accéder à :

```
http://localhost:8080/bonjour
```

👉 L’application fonctionne maintenant **dans un conteneur Docker**.

---

## ✅ Conclusion

Ce TP montre :

* L’exécution d’une application Java **sans Docker**
* La création d’un **JAR exécutable** avec Gradle
* La **dockerisation** de l’application
* L’exposition d’une API REST via Docker

Le projet est désormais **portable, reproductible et prêt pour le déploiement** 🚀
