# EcoTrack Analytics

Une organisation souhaite analyser des séries de mesures et conserver les résultats. Depuis une page React, l’utilisateur lance un calcul. L’API Node.js demande au service Python de calculer la moyenne et le maximum, puis enregistre le résultat dans PostgreSQL. La page affiche le résultat et l’historique.

```mermaid
flowchart TD
    F["Frontend React"] -->|"Requête depuis le navigateur"| A["API Node.js"]
    A -->|"Nombres"| W["Worker Python"]
    W -->|"Moyenne et maximum"| A
    A -->|"Sauvegarde et historique"| D["PostgreSQL"]
    A -->|"Résultat et historique"| F
```

Le JavaScript React s’exécute dans le navigateur. Le conteneur frontend sert les fichiers de la page. Le navigateur appelle localhost:5000 ; dans Docker, l’API contacte worker et db par leur nom de service.

## Votre mission

### 1. Identifier les services

| Dossier ou service | Technologie | Commande de lancement | Port |
|---|---|---|---|
| frontend | React et serveur Node.js | npm run build puis npm start | 3000 |
| api | Node.js Express | npm start | 5000 |
| worker | Python Flask | python app.py | 8000 |
| db | Image postgres:15-alpine | À configurer dans Compose | 5432 interne |


### 2. Tester Python avec un venv

Windows PowerShell :

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Linux ou macOS :

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Puis :

```bash
python -m pip install -r requirements.txt
python -c "import sys; print(sys.executable)"
python app.py
```

Le chemin Python doit contenir .venv. Ouvrir http://localhost:8000/health : résultat attendu {"status":"ok"}.


### 3. Créer les images

### 4. Configurer Compose

### 5. Ajouter le secret

### 6. Lancer et vérifier

À la racine, après création des fichiers Docker :

```bash
docker compose config -q
docker compose up -d --build
docker compose ps -a
```

Ouvrir http://localhost:3000 pour l’interface et http://localhost:5000/status pour vérifier les trois services backend.


Si nécessaire :

```bash
docker compose logs api
docker compose logs worker
docker compose logs db
docker compose logs frontend
```

### 7. Compléter le dépôt et remettre

Inclure le code fourni, les trois Dockerfiles, les .dockerignore, compose.yaml, requirements.txt, .gitignore et ce README complété.

Ajouter ici :

- Les noms du binôme et le groupe.
- Vos commandes de lancement et d’arrêt.
- Vos preuves : commande ou action, résultat observé et explication.
- Les vérifications du calcul, de la persistance, des réseaux et du secret.

Exclure db_password.txt, .env, .venv et node_modules. Après un nouveau clonage, un lecteur doit pouvoir recréer le secret et démarrer l’application.

Déposer le lien du dépôt GitLab dans le devoir Moodle avant l’échéance annoncée. Donner accès à l’enseignante.

### Endpoints fournis

| Service | Méthode et chemin | Rôle |
|---|---|---|
| worker | GET /health | Vérifier Python |
| worker | POST /analyze | Recevoir {"numbers":[10,20,30]} et calculer |
| api | GET /health | Vérifier le processus Node.js |
| api | GET /status | Vérifier PostgreSQL et worker |
| api | POST /analyze | Calculer via Python et enregistrer |
| api | GET /history | Lire les 50 derniers résultats |

___

### GROUPE : Julien SAUGUES--EYRAUD et Mathis GILGENKRANTZ (A1)
___

### ### COMMANDES DE LANCEMENT ET D'ARRÊT
___

Toutes les commandes doivent être exécutées depuis la racine du projet.

**Vérifier la configuration :**
```bash
docker compose config -q
```

**Construire les images et démarrer les services :**
```bash
docker compose up -d --build
```

**Vérifier l'état des conteneurs :**
```bash
docker compose ps -a
```

**Consulter les logs :**
```bash
docker compose logs api
docker compose logs worker
docker compose logs db
docker compose logs frontend
```

**Arrêter les services :**
```bash
docker compose down
```

Les données PostgreSQL sont conservées dans un volume Docker et restent disponibles après l'arrêt des conteneurs.

### PREUVES
___

**1. Test de l'environnement Python avec un venv**

![Test Python avec venv](images_preuves/partie2.png)


**2. Vérification du frontend**

![Frontend](images_preuves/frontend.png)

L'interface React est accessible depuis le navigateur à l'adresse http://localhost:3000.

**3. Vérification des services backend**

![Statut backend](images_preuves/status.png)

L'endpoint `/status` permet de vérifier la communication entre l'API, le worker Python et PostgreSQL.

### VÉRIFICATIONS
___

**1. Vérification du calcul**

Une requête POST `/analyze` est envoyée avec les données suivantes :

```json
{
  "numbers": [10, 20, 30]
}
```

Résultats attendus :
- Moyenne : 20
- Maximum : 30

L'API transmet les nombres au worker Python, récupère les résultats et les enregistre dans PostgreSQL.

Voilà le résultat sur l'interface React : ![alt text](images_preuves/analyse.png)

**2. Vérification de la persistance**

Après avoir effectué plusieurs calculs, l'endpoint GET `/history` permet de consulter les résultats enregistrés.

Les conteneurs sont ensuite arrêtés puis redémarrés avec Docker Compose.

Le volume PostgreSQL permet de conserver les résultats même après la suppression des conteneurs.

Voilà le résultat sur l'interface React après avoir arreté puis redémarré les conteneurs (docker compose down puis docker compose up -d) : ![alt text](images_preuves/arret_conteneurs.png)

**3. Vérification des réseaux**

Les différents services communiquent entre eux grâce au réseau créé automatiquement par Docker Compose.

L'API utilise les noms de services pour communiquer avec les autres conteneurs :
- `worker:8000` pour le service Python.
- `db:5432` pour PostgreSQL.

**4. Vérification du secret**

Le mot de passe PostgreSQL est stocké dans le fichier `db_password.txt`.

Docker Compose transmet ce fichier aux conteneurs concernés grâce au mécanisme des secrets.

Le fichier `db_password.txt` est exclu du dépôt Git grâce au `.gitignore`.