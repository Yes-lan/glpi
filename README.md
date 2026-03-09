# GLPI sous Docker

![Aperçu GLPI](captures/Capture%20d%E2%80%99%C3%A9cran%202026-03-09%20092041.png)

Installation complète de GLPI avec Docker sur Windows.

## 1) Prérequis

- Docker Desktop installé et démarré.
- WSL2 activé (recommandé).
- Port `8080` disponible.

Vérifications rapides :

```powershell
docker --version
docker compose version
docker info
```

## 2) Lancer GLPI

Depuis le dossier du projet :

```powershell
docker compose pull
docker compose up -d
docker compose ps
```

Ouvrir ensuite :

- http://localhost:8080

## 3) Installation web GLPI

1. Choisir la langue.
2. Accepter la licence.
3. Cliquer sur **Installer**.
4. Renseigner la base de données :
   - Hôte : `mysql`
   - Base : `glpi`
   - Utilisateur : `glpi`
   - Mot de passe : `glpi`
5. Terminer l’assistant.

## 4) Identifiants par défaut GLPI

- Admin : `glpi` / `glpi`
- Technicien : `tech` / `tech`
- Utilisateur : `normal` / `normal`
- Post-only : `post-only` / `postonly`

Change les mots de passe par défaut immédiatement après la première connexion.

## 5) Commandes utiles

```powershell
docker compose logs -f glpi
docker compose logs -f db
docker compose restart
docker compose down
```

## 6) Sauvegarde rapide (SQL)

```powershell
docker compose exec db sh -c "mysqldump -u$MYSQL_USER -p$MYSQL_PASSWORD $MYSQL_DATABASE" > glpi-backup.sql
```

## 7) Captures

Place tes captures dans le dossier `captures/`.

Références utiles :

- [Guide détaillé](INSTALL_GLPI_DOCKER.md)
- [Checklist captures](captures/README.md)