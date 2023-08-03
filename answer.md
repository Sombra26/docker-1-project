### 2

- Explication des lignes du fichier docker-compose.yaml

```
version: "3.8"
services:

# Lorsque le fichier Docker compose va démarrer le conteneur, 😀
# le fichier Docker File sera automatiquement utilisé pour la configuration 😀
  app:
    build: .
    command: python -u app.py
    ports:
      - "5000:5000"
      
    # le "." représente le répertoire sur lequel nous sommnes 😀
    # Le chemin du répertoire est dans le conteneur 😀
    volumes:
      - .:/app
    links:
      - db
    
    # Le db est la configuration du conteneur de la base de données. 😀
  db:
    image: mongo:latest 
    hostname: test_mongodb 
    environment:
      - MONGO_INITDB_DATABASE=animal_db
      - MONGO_INITDB_ROOT_USERNAME=root
      - MONGO_INITDB_ROOT_PASSWORD=pass
    # les variables d'envorionnements sont configurées dans le fichier Dockerfile 😀
    # ce qui permettra à l'application Node de les utiliser se connecter à la base de données 😀

    # Lorsque le conteneur MongoDB va daémarrer, 😀
    # le script va se lancer dans le répertoire du conteneur 'docker-entrypoint-initdb.d' 😀
    # qui va se connecter au fichier de départ. 😀
    volumes:
      - ./init-db.js:/docker-entrypoint-initdb.d/init-db.js:ro

    # Le port correspond à la base de donnée MongoDB 😀
    ports: 
      - 27017:27017
```

### 3

- Vérification de la commande README.md permettant de bâtir l'image et d'accéder à l'application de l'URL http://localhost:5000/animals


![image](https://github.com/Sombra26/docker-1-project-part-2/assets/108517832/8c06855f-97c5-4531-9440-342658e0a1e4)

### 4

- Réécrire deux Dockerfiles et donner les commandes qui permettent de lancer cette même application sans utiliser docker compose

- Docker file-app

```
FROM python:3.9-slim

WORKDIR /app

COPY app.py .
COPY requirements-app.txt .


RUN pip install -r requirements-app.txt

CMD ["python3", "-u","app.py"] 😀
```
- Dockerfile-db

```
FROM python:3.9-slim

WORKDIR /db

ENV MONGO_INITDB_DATABASE="animal_db"
ENV MONGO_INITDB_ROOT_USERNAME="root"
ENV ONGO_INITDB_ROOT_PASSWORD="pass"

COPY app.py .
COPY requirements-db.txt .

RUN pip install -r requirements-db.txt
```
- Nous avons créer deux fichiers requirements.txt pour différencier leur utilité en fontion des apps.
- requirements-app.txt
```
flask
```

- requirements-db.txt
```
flask
pymongo
```
- Voici les commandes éxécuter pour lancer les applications : 

- Création des volumes 
```
docker build -t app -f Dockerfile-app .
docker build -t db -f Dockerfile-db .
```

- Lancement des applications

```
docker run -p 27017:27017 -v $pwd/db:/usr/src/db/python mongo
docker run -p 5000:5000 -v $pwd/app:/usr/src/app/python app
```
