---
title: Générer une application web Python et PostgreSQL dans Azure App Service | Microsoft Docs
description: Découvrez comment exécuter une application Python orientée données dans Azure avec une connexion à une base de données PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435732"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Créer une application web Docker Python et PostgreSQL dans Azure

[App Service sur Linux](app-service-linux-intro.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce didacticiel montre comment créer une application web Python orientée données, à l’aide de PostgreSQL en tant que le serveur principal de base de données. Cela fait, vous disposez d’une application Python Flask s’exécutant dans un conteneur Docker sur App Service sur Linux.

![Application Docker Python Flask dans App Service sur Linux](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connecter une application Python à PostgreSQL
> * Déploiement de l’application dans Azure
> * Afficher les journaux de diagnostic
> * Mise à jour du modèle de données et redéploiement de l’application
> * Gérer l’application dans le portail Azure

Vous pouvez suivre les étapes de cet article sur MacOS. Les instructions pour Linux et Windows sont identiques dans la plupart des cas, mais les différences ne sont pas détaillées dans ce didacticiel.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

1. [Installez Git](https://git-scm.com/)
1. [Installez Python](https://www.python.org/downloads/)
1. [Téléchargez et exécutez PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Test de l’installation PostgreSQL locale et création d’une base de données

Dans une fenêtre de terminal locale, exécutez `psql` pour vous connecter à votre serveur PostgreSQL local.

```bash
sudo -u postgres psql postgres
```

Si vous obtenez un message d’erreur semblable à `unknown user: postgres`, votre installation de PostgreSQL est peut-être configurée avec votre nom d’utilisateur connecté. Essayez plutôt la commande suivante.

```bash
psql postgres
```

Si la connexion est établie, cela signifie que votre base de données PostgreSQL est en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données PostgresQL est démarrée en suivant les étapes relatives à votre système d’exploitation dans la page [Téléchargements - Distribution principale de PostgreSQL](https://www.postgresql.org/download/).

Créez une base de données appelée *eventregistration* et configurez un utilisateur de base de données nommé *manager* avec le mot de passe *supersecretpass*.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Créer une application Python locale

Cette étape consiste à configurer le projet Python Flask local.

### <a name="clone-the-sample-app"></a>Clonage de l’exemple d’application

Ouvrez la fenêtre de terminal et entrez `CD` pour accéder à un répertoire de travail.

Exécutez les commandes suivantes pour cloner l’exemple de référentiel.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

Cet exemple de référentiel contient une application [Flask](http://flask.pocoo.org/).

### <a name="run-the-app-locally"></a>Exécutez l’application localement.

Installez les packages requis et démarrez l’application.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Lorsque l’application est entièrement chargée, vous obtenez un message similaire à celui-ci :

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Dans un navigateur, accédez à `http://localhost:5000`. Cliquez sur **S’inscrire** et créez un utilisateur de test.

![Application Python Flask s’exécutant localement](./media/tutorial-python-postgresql-app/local-app.png)

L’exemple d’application Flask stocke les données utilisateur dans la base de données. Si vous parvenez à inscrire un utilisateur, votre application écrit les données dans la base de données PostgreSQL locale.

À tout moment, pour arrêter le serveur Flask, tapez Ctrl+C dans le terminal.

## <a name="create-a-production-postgresql-database"></a>Création d’une base de données de production PostgreSQL

Dans cette étape, vous allez créer une base de données PostgreSQL dans Azure. Lorsque votre application est déployée sur Azure, elle utilise cette base de données cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur Azure Database pour PostgreSQL

Créez un serveur PostgreSQL avec la commande [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) dans Cloud Shell.

Dans l’exemple de commande suivant, remplacez *\<postgresql_name>* par un nom de serveur unique, puis remplacez *\<admin_username>* et *\<admin_password>* par les informations d’identification de l’utilisateur de votre choix. Les informations d’identification sont associées au compte administrateur de base de données. Le nom de serveur est utilisé dans votre point de terminaison PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`). C’est pourquoi, il doit être unique parmi l’ensemble des serveurs dans Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
```

Lorsque le serveur de base de données Azure pour PostgreSQL est créé, l’interface Azure CLI affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> N’oubliez pas \<admin_username> et \<admin_password> pour une utilisation ultérieure. Vous en aurez besoin pour vous connecter au serveur Postgre et à ses bases de données.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Créer des règles de pare-feu pour le serveur PostgreSQL

Dans Cloud Shell, exécutez les commandes Azure CLI suivantes pour autoriser l’accès à la base de données à partir des ressources Azure.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Ce paramètre autorise les connexions de réseau à partir de toutes les adresses IP au sein du réseau Azure. À des fins de production, essayez de configurer les règles de pare-feu les plus restrictives possibles en [utilisant uniquement les adresses IP sortantes utilisées par votre application](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Dans Cloud Shell, exécutez à nouveau la commande pour autoriser l’accès à partir de votre ordinateur local en remplaçant *\<your_ip_address>* par [votre adresse IP IPv4 locale](http://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Connecter une application Python à la base de données de production

À cette étape, vous connectez votre exemple d’application Flask au serveur Azure Database pour PostgreSQL que vous avez créé.

### <a name="create-empty-database-and-user-access"></a>Créer une base de données vide et un accès utilisateur

Dans la fenêtre de terminal local, connectez-vous à la base de données en exécutant la commande ci-dessous. Lorsque vous êtes invité à saisir votre mot de passe administrateur, utilisez celui que vous avez spécifié pour la [création d’un serveur Azure Database pour PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Comme dans votre serveur Postgres local, créez la base de données et l’utilisateur dans le serveur Postgres Azure.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

> [!NOTE]
> Il est recommandé de créer des utilisateurs de base de données avec des autorisations restreintes pour des applications spécifiques, au lieu de faire appel à l’utilisateur administrateur. Dans cet exemple, l’utilisateur `manager` dispose de privilèges complets _uniquement_ pour la base de données `eventregistration`.

### <a name="test-app-connectivity-to-production-database"></a>Tester la connectivité de l’application à base de données de production

De retour dans la fenêtre de terminal locale, saisissez les commandes suivantes pour exécuter la migration de base de données Flask et le serveur Flask.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Lorsque l’application est entièrement chargée, vous obtenez un message similaire à celui-ci :

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Dans un navigateur, accédez à http://localhost:5000. Cliquez sur **S’inscrire** et créez une inscription de test. Vous écrivez maintenant des données dans la base de données dans Azure.

![Application Python Flask s’exécutant localement](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Déployer dans Azure

Dans cette étape, vous allez déployer l’application Python connectée à Postgres dans Azure App Service.

### <a name="configure-repository"></a>Configurer le référentiel

Le moteur de déploiement Git dans App Service appelle l’automatisation `pip` lorsqu’un fichier _application.py_ se trouve à la racine du référentiel. Dans ce didacticiel, vous allez laisser le moteur de déploiement exécuter l’automatisation pour vous. Dans la fenêtre de terminal local, accédez à la racine du référentiel, créer un fichier _application.py_ factice et validez vos modifications.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Créer un plan App Service 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Plus haut dans ce didacticiel, vous avez défini des variables d’environnement pour vous connecter à votre base de données PostgreSQL.

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) dans Cloud Shell.

L’exemple suivant spécifie les informations de connexion à la base de données comme des paramètres d’application. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 5, done. 
Delta compression using up to 4 threads. 
Compressing objects: 100% (5/5), done. 
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done. 
Total 5 (delta 3), reused 0 (delta 0) 
remote: Updating branch 'master'. 
remote: Updating submodules. 
remote: Preparing deployment for commit id '6c7c716eee'. 
remote: Running custom deployment command... 
remote: Running deployment command... 
remote: Handling node.js deployment. 
. 
. 
. 
remote: Deployment successful. 
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
 * [new branch]      master -> master 
```  

### <a name="configure-entry-point"></a>Configurer le point d’entrée

Par défaut, l’image intégrée recherche un fichier _wsgi.py_ ou _application.py_ dans le même dossier racine que le point d’entrée, mais votre point d’entrée est le fichier _app/app.py_. Le fichier _application.py_ que vous avez ajouté précédemment est vide et ne fait rien.

Dans Cloud Shell, exécutez la commande [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) pour définir un script de démarrage personnalisé.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

Le paramètre `--startup-file` accepte une commande personnalisée ou le chemin d’accès au fichier qui contient la commande personnalisée. Votre commande personnalisée doit suivre ce format :

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

Dans la commande personnalisée, `--chdir` est requis si votre point d’entrée n’est pas dans le dossier racine, et si `<subdirectory>` est le sous-répertoire. `<module>` est le nom du fichier _.py_ et `<variable>` est la variable dans le module qui représente votre application web.

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure

Accédez à l’application web déployée. L’application web met un certain temps à démarrer, car le conteneur doit être téléchargé et exécuté lors de la première sollicitation de l’application. Si la page expire ou affiche un message d’erreur, patientez quelques minutes et actualisez la page.

```bash
http://<app_name>.azurewebsites.net
```

Vous verrez des invités déjà inscrits, qui ont été enregistrés dans la base de données de production Azure à l’étape précédente.

![Application Python Flask s’exécutant dans Azure](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Félicitations !** Vous exécutez une application Python dans App Service pour Linux.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

Étant donné que l’application Python s’exécute dans un conteneur, App Service sur Linux vous permet d’accéder aux journaux de console générés à partir du conteneur. Pour rechercher les journaux, accédez à cette URL :

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Vous devez voir deux objets JSON, chacun disposant d’une propriété `href`. Une propriété `href` pointe vers les journaux de console Docker (se termine par `_docker.log`) et l’autre `href` pointe vers les journaux de console générés à l’intérieur du conteneur Python. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Copiez la valeur `href` souhaitée dans une fenêtre de navigateur pour accéder aux journaux. Les journaux ne sont pas transmis en continu. Il est possible que vous deviez patienter. Pour afficher les nouveaux journaux, actualisez la page du navigateur.

## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, vous ajoutez le nombre de participants à chaque inscription d’événement en mettant à jour le modèle `Guest`, puis vous redéployez la mise à jour vers Azure.

Dans la fenêtre de terminal local, vérifiez les fichiers dans la branche `modelChange` avec la commande git suivante :

```bash
git checkout origin/modelChange -- .
```

Cette validation apporte déjà les modifications nécessaires aux vues, aux contrôleurs et au modèle. Elle inclut également une migration de base de données générée via *alembic* (`flask db migrate`). Vous pouvez voir toutes les modifications par le biais de la commande git suivante :

```bash
git diff master origin/modelChange
```

### <a name="test-your-changes-locally"></a>Tester vos modifications en local

Dans la fenêtre de terminal locale, exécutez les commandes suivantes pour tester les modifications en local en exécutant le serveur Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Accédez à http://localhost:5000 dans votre navigateur pour afficher les modifications. Créez une inscription de test.

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Dans la fenêtre du terminal local, validez toutes les modifications dans Git, puis envoyez les modifications de code à Azure.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Accédez à votre application web Azure et essayez de nouveau la nouvelle fonctionnalité. Assurez-vous d’actualiser la page.

```bash
http://<app_name>.azurewebsites.net
```

![Application Docker Python Flask dans Azure App Service](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Gérer votre application web dans le portail Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application web. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connecter une application Python à PostgreSQL
> * Déploiement de l’application dans Azure
> * Afficher les journaux de diagnostic
> * Mise à jour du modèle de données et redéploiement de l’application
> * Gérer l’application dans le portail Azure

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Configurer une image Python intégrée](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](../app-service-web-tutorial-custom-domain.md)

