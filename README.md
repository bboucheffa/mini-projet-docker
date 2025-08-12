# 📚 Student List – Dockerized Application

## 1️⃣ Contexte
**POZOS** est une société française spécialisée dans le développement de logiciels pour les lycées.  
L'objectif de ce Proof of Concept (POC) est de moderniser l'infrastructure existante afin de la rendre plus **scalable** et **facile à déployer** grâce à Docker et Docker Compose.  

L'application `student_list` permet d'afficher la liste des étudiants et leurs âges via :
- **Une API Flask** (avec authentification Basic Auth)
- **Une interface web PHP** pour la consultation

---

## 2️⃣ Objectifs
- Conteneuriser l'API et le site web séparément
- Permettre la communication entre les services via un réseau Docker dédié
- Déployer un **registre Docker privé** pour héberger les images
- Appliquer les bonnes pratiques **Infrastructure as Code**

---

## 3️⃣ Étapes réalisées

### 3.1 Création de l'image API
- Base : `python:3.11-slim`
- Installation des dépendances système et Python (`Flask`, `flask_httpauth`, etc.)
- Création des `requirements.txt` et `student_age.py`
- Exposition du port `5000`
- Volume `/data` pour `student_age.json`
- Commande de démarrage :  
  ```bash
  CMD ["python3", "./student_age.py"]
  ```

### 3.2 Test de l'API
  ```bash
  docker build -t student-api .
  docker run --rm --name student-list -dp 5000:5000 -v ./student_age.json:/data/student_age.json api:v1.0
  curl -u toto:python -X GET http://192.168.56.5:5000/pozos/api/v1.0/get_student_ages
```

### 3.3 Déploiement via Docker Compose
docker-compose.yml déploie :
- api : image de l’API construite précédemment
- website : php:apache avec variables d’environnement USERNAME et PASSWORD pour l’API
Lancement :
```bash
docker compose -f docker-compose.yml up -d
```

### 3.4 Mise en place d’un registre Docker privé
- Service : registry:2.8.1 avec authentification basique (htpasswd)
- Interface Web : joxit/docker-registry-ui:2 sur port 8080
- Création d’utilisateur :
```bash
htpasswd -Bbn pozos pozos > /auth/htpasswd
```
- Connexion au registre :
```bash
docker login -u pozos -p pozos http://localhost:8080
```
- Push d’une image :
```bash
docker tag api:v1.0 localhost:8080/api:v1.0
docker push localhost:8080/api:v1.0
```

## 4️⃣ Résultat:
- API et site web fonctionnent en containers séparés et communiquent via un réseau Docker dédié.
- Les images sont versionnées et stockées dans un registre privé accessible via interface web.
