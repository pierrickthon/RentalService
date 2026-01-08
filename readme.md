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



# TP 3 : Kubernetes 

Réalisation du TP 3 stipulant sur les initiations de Kubernetes et sur sa bonne utilisation. 

1) Céation du déploiement de Kubernetes à partir de mon image Docker 

J'ai décidé de prendre l'image du Customer Service car meilleure que celui de RentalService et surtout plus récente.

Premier probleme : Quand je lance la commande "minikube start", j'ai immédiatement une erreure lié à l'utilisation de VirtualBox.

![VirtualBox](images/VirtualBox.png)

Pour régler ce problme, j'ai dû lier le lancement de minikube à Docker avec la commande : 

```bash
minikube start --driver=docker
```

Une fois cela fait, on obtient : 

![Kubernetes](images/Kubernetes.png)

Parfait !!! Maintenant Kubernetes marche et est bine configuré. 

On va pouvoir passer à la suite du TP. Pour créer le déploiement j'utilise la commande : 

```bash
kubectl create deployment mon-application --image=yannthon/service2-php
```

On obtient le résultat escompté : 

![Déploiement](images/Déploiement_images_kubernetes.png)

On vérifie bien que le déploiement a eu lieu avec les commandes : 

```kubectl get deployments```
et
```kubectl get pods```

Le résultat en image : 

![Vérification_déploiement](images/vérification_deploiements.png)

2) Exposez les routes HTTP et HTTPS via NodePort.

Maintenant on va exposer les routes HTTP et HTTPS. Pour ce faire, on va créer un service de type ClusterIP. Avec la commande : 

```bash 
kubectl expose deployment mon-application --port=80 --target-port=8080
```
On vérifie que cela à bien été fait avec la commande : ```kubectl get services```

Preuve en image :

![exposition](images/exposition_verification.png)

Étant donné que j'ai lancé l'exposition des routes avec le port 80 et non le "NodePort", la commande ```minikube service myservice --url``` me renvoie ceci comme message : "Because you are using a Docker driver on darwin, the terminal needs to be open to run it." Donc je n'arrive pas à avoir le message : "hello"

On va démarrer une deuxieme instance : 

```bash 
kubectl scale --replicas=2 deployment/mon-application
```
Voici le résultat : 

![deuxieme_instance](images/deuxieme_instance.png)

3) Création d'un service de type LoadBalancer

Pour créer un nouveau service de type LoadBalancer, on va d'abord devoir supprimer l'ancien avec la commade ```kubectl delete service <nomduservice>```

![LoadBalancer](images/LoadBalancer.png)

Maintenant, on va mettre à jour, l'image de l'application. Pour ce faire on va utiliser la commande : ```kubectl set image deployments/my-deployment my-deployment=dockerHudId/my-image:v2```

Petit probleme : Je n'arrive pas à mettre à jour l'image car le systeme n'arrive pas à trouver le bon container. Je dois donc trouver d'abord le bon container avec la commande : 

```bash 
kubectl describe deployment mon-application
```
![containers](images/Conteneurs.png)

Une fois que c'est bon, on peut poursuivre. 

![MAJ](images/MAJ.png)

5) Créez un déploiement et un service à l'aide d'un fichier YAML

Pour cela, j'ai du créer deux fichiers : myservice-deployment.yml et myservice-service.yml. 

![fichiersyaml](images/fichiers_yaml.png)

