
# Étapes pour Configurer Flask avec Nginx et Gunicorn
Installer Gunicorn : C’est un serveur d’application pour exécuter Flask.
```
pip install gunicorn

```
# Lancer votre Application avec Gunicorn : 
***Lancez votre application Flask avec Gunicorn. Par exemple :***
```
gunicorn -w 4 -b 127.0.0.1:8000 app:app

```

***Ici, -w 4 signifie que vous lancez 4 "workers" (processus).*** 
Plus il y a de workers, plus votre application peut gérer de requêtes en même temps.

Configurer Nginx :

 - Installez Nginx et ouvrez le fichier de configuration pour créer un "bloc serveur" (c’est comme un fichier de paramètres pour votre application).

- Configurez Nginx pour rediriger le trafic vers Gunicorn en utilisant une configuration comme ceci :

nginx
```
server {
    listen 80;
    server_name monsite.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```
# Démarrer Nginx : 
***Relancez Nginx pour appliquer la configuration :***

```
sudo systemctl restart nginx
```

# En Résumé

- ***Flask : Permet de construire les pages dynamiques et d’accéder aux données.***

- ***Gunicorn : Exécute votre code Flask en tant que serveur d’application.***

- ***Nginx : Gère les connexions des utilisateurs, protège votre application, et redirige les requêtes vers Gunicorn pour exécuter Flask.***


# Architecture

```
customers_monitoring/
├── app.py
├── wsgi.py
├── templates/
│   └── index.html
└── requirements.txt

```


Tester Flask Localement :

Démarrez votre application avec Flask en mode développement pour vérifier que tout fonctionne.

***python app.py***

Vous devriez voir ***"Bienvenue sur le portail client !"*** dans le navigateur à l’adresse http://127.0.0.1:5000

# Lancer Flask avec Gunicorn
Pour exécuter Flask en mode production, utilisez Gunicorn.
```
gunicorn -w 4 -b 127.0.0.1:8000 wsgi:app
```
Cela lance l’application avec 4 workers, et elle est accessible localement à l’adresse http://127.0.0.1:8000.

# Configurer Nginx
Nginx va maintenant agir en tant que "reverse proxy" et rediriger les requêtes vers Gunicorn.

# Installer Nginx :


```
sudo apt update
sudo apt install nginx
```
Créer un Fichier de Configuration pour Nginx :

Créez un nouveau fichier de configuration dans /etc/nginx/sites-available/customers_monitoring/:


```
server {
    listen 80;
    server_name monsite.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
# Activer la Configuration de Nginx :

Liez le fichier de configuration à sites-enabled :
bash

```
sudo ln -s /etc/nginx/sites-available/customers_monitoring /etc/nginx/sites-enabled
```
Relancez Nginx pour appliquer la configuration :


```
sudo systemctl restart nginx
```
Accéder à l’Application :

Si tout est configuré correctement, vous devriez pouvoir accéder à votre application à l’adresse http://monsite.com (ou à localhost si vous êtes en local).

# En Résumé
- Flask : Crée l’application. 

- Gunicorn : Exécute l’application en mode production.
Nginx : Gère les connexions et redirige les requêtes vers Gunicorn.
