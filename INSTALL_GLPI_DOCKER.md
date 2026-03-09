# Installation détaillée de GLPI avec Docker (Windows)

Ce guide est adapté à ton projet dans `c:\Users\npichon\Desktop\glpi`.

## 1) Prérequis

- Docker Desktop installé et démarré.
- WSL2 activé (recommandé par Docker Desktop).
- Ports disponibles : `8080` (GLPI) et `3306` interne Docker.
- Au moins 4 Go de RAM alloués à Docker.

Vérifications rapides dans PowerShell :

```powershell
docker --version
docker compose version
docker info
```

---

## 2) Fichiers de base

Ton `docker-compose.yaml` contient déjà les services nécessaires :

- `glpi` : image `glpi/glpi:latest`
- `db` : image `mysql`
- Volumes persistants : `glpi_data`, `db_data`

Le `Dockerfile` présent dans le dossier n’est pas nécessaire pour lancer GLPI via ce `docker-compose.yaml` (il semble appartenir à un autre projet/app PHP).

---

## 3) Démarrage de la stack

Depuis `c:\Users\npichon\Desktop\glpi` :

```powershell
docker compose pull
docker compose up -d
```

Contrôles :

```powershell
docker compose ps
docker compose logs -f glpi
docker compose logs -f db
```

Quand GLPI est prêt, ouvre :

- http://localhost:8080

---

## 4) Installation initiale de GLPI (interface web)

1. Choisis la langue.
2. Accepte la licence.
3. Sélectionne **Installer**.
4. Paramètres BDD :
   - Serveur : `mysql`
   - Base : `glpi` (ou valeur de `DB_NAME`)
   - Utilisateur : `glpi` (ou valeur de `DB_USER`)
   - Mot de passe : `glpi` (ou valeur de `DB_PASSWORD`)
5. Lance l’initialisation.
6. Connecte-toi avec les comptes par défaut GLPI (puis change les mots de passe immédiatement).

---

## 5) Comptes et sécurité (obligatoire)

Après la première connexion :

- Change le mot de passe de tous les comptes par défaut.
- Crée un compte administrateur nominatif.
- Supprime/désactive les comptes inutiles.
- Active HTTPS si exposition hors local (reverse proxy recommandé).

Pour un environnement de prod, évite `latest` et fixe les versions d’images.

---

## 6) Persistance et sauvegardes

Données persistées dans les volumes Docker :

- `glpi_data`
- `db_data`

Lister les volumes :

```powershell
docker volume ls
```

Sauvegarde SQL (exemple) :

```powershell
docker compose exec db sh -c "mysqldump -u$MYSQL_USER -p$MYSQL_PASSWORD $MYSQL_DATABASE" > glpi-backup.sql
```

Restaurer SQL (exemple) :

```powershell
Get-Content .\glpi-backup.sql | docker compose exec -T db sh -c "mysql -u$MYSQL_USER -p$MYSQL_PASSWORD $MYSQL_DATABASE"
```

---

## 7) Mise à jour

```powershell
docker compose pull
docker compose up -d
```

Puis vérifie l’interface GLPI et applique les migrations proposées.

---

## 8) Dépannage courant

### GLPI ne s’ouvre pas

- Vérifie que `docker compose ps` montre `glpi` en `running`.
- Vérifie les logs : `docker compose logs glpi --tail=200`.
- Vérifie qu’aucun autre service n’utilise le port `8080`.

### Erreur DB à l’installation

- Attends 15-30s après le démarrage de `db`.
- Vérifie les variables `DB_NAME`, `DB_USER`, `DB_PASSWORD`.
- Vérifie l’hôte `mysql` (alias réseau du service `db`).

### Permissions/écriture

- Redémarre : `docker compose restart`.
- En dernier recours : recrée la stack (attention aux données) :

```powershell
docker compose down
docker compose up -d
```

---

## 9) Commandes utiles

```powershell
docker compose up -d
docker compose down
docker compose restart
docker compose logs -f
docker compose exec glpi sh
docker compose exec db sh
```

---

## 10) Captures d’écran à produire (checklist)

Ajoute tes captures dans le dossier `captures/` avec les noms suivants :

1. `01-page-accueil-installation.png`
2. `02-parametres-base-de-donnees.png`
3. `03-initialisation-terminee.png`
4. `04-ecran-connexion-glpi.png`
5. `05-dashboard-admin.png`
6. `06-configuration-generale.png`

Sur Windows :

- `Win + Shift + S` pour capturer.
- Colle dans Paint puis enregistre dans `captures/`.

---

## 11) Variante recommandée (optionnelle) : fichier `.env`

Tu peux créer un `.env` pour ne pas laisser les mots de passe en clair dans le compose :

```env
DB_NAME=glpi
DB_USER=glpi
DB_PASSWORD=change_me
```

Ensuite redémarre :

```powershell
docker compose up -d
```

---

## 12) Résumé rapide

- Lancement : `docker compose up -d`
- URL : http://localhost:8080
- DB host : `mysql`
- Données persistées : volumes Docker
- Pense à sécuriser les comptes par défaut immédiatement
