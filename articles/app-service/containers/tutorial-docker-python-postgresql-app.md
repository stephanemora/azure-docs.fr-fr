---
title: Générer une application web Python et PostgreSQL dans Azure App Service | Microsoft Docs
description: Découvrez comment exécuter une application Python orientée données dans Azure avec une connexion à une base de données PostgreSQL.
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 9a623156ad2a27abf7fa5e865f8b7452e2c70b3c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124516"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Créer une application web Docker Python et PostgreSQL dans Azure

Web App pour conteneurs offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce didacticiel montre comment créer une application web Python orientée données, à l’aide de PostgreSQL en tant que serveur principal de base de données. Ceci fait, vous disposez d’une application Python Flask s’exécutant dans un conteneur Docker sur [App Service sur Linux](app-service-linux-intro.md).

![Application Docker Python Flask dans App Service sur Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connecter une application Python à PostgreSQL
> * Déploiement de l’application dans Azure
> * Mise à jour du modèle de données et redéploiement de l’application
> * Gérer l’application dans le portail Azure

Vous pouvez suivre les étapes de cet article sur MacOS. Les instructions pour Linux et Windows sont identiques dans la plupart des cas, mais les différences ne sont pas détaillées dans ce didacticiel.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

1. [Installez Git](https://git-scm.com/)
1. [Installez Python](https://www.python.org/downloads/)
1. [Téléchargez et exécutez PostgreSQL](https://www.postgresql.org/download/)
1. [Installez Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Test de l’installation PostgreSQL locale et création d’une base de données

Dans une fenêtre de terminal locale, exécutez `psql` pour vous connecter à votre serveur PostgreSQL local.

```bash
sudo -u postgres psql
```

Si la connexion est établie, cela signifie que votre base de données PostgreSQL est en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données PostgresQL est démarrée en suivant les étapes décrites dans la page [Downloads - PostgreSQL Core Distribution (Téléchargements - Distribution principale de PostgreSQL)](https://www.postgresql.org/download/).

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

Exécutez les commandes suivantes pour cloner l’exemple de référentiel, puis accédez à la version *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Cet exemple de référentiel contient une application [Flask](http://flask.pocoo.org/). 

### <a name="run-the-app-locally"></a>Exécutez l’application localement.

Installez les packages requis et démarrez l’application.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![Application Python Flask s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-app.png)

L’exemple d’application Flask stocke les données utilisateur dans la base de données. Si vous parvenez à inscrire un utilisateur, votre application écrit les données dans la base de données PostgreSQL locale.

À tout moment, pour arrêter le serveur Flask, tapez Ctrl+C dans le terminal. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Création d’une base de données de production PostgreSQL

Dans cette étape, vous allez créer une base de données PostgreSQL dans Azure. Lorsque votre application est déployée sur Azure, elle utilise cette base de données cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur Azure Database pour PostgreSQL

Créez un serveur PostgreSQL avec la commande [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) dans Cloud Shell.

Dans l’exemple de commande suivant, remplacez *\<postgresql_name>* par un nom de serveur unique, puis remplacez *\<admin_username>* et *\<admin_password>* par les informations d’identification de l’utilisateur de votre choix. Le nom de serveur est utilisé dans votre point de terminaison PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`). C’est pourquoi, il doit être unique parmi l’ensemble des serveurs dans Azure. Les informations d’identification sont associées au compte utilisateur de l’administrateur de base de données. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
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
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>Créer une règle de pare-feu pour le serveur PostgreSQL

Dans Cloud Shell, exécutez la commande Azure CLI suivante pour autoriser l’accès à la base de données à partir de toutes les adresses IP. 
> [!Note]
> Il est déconseillé de laisser tous les ports ouverts pour votre base de données ou de rendre votre base de données accessible sur Internet.  Pour sécuriser correctement votre nouvelle base de données en vue d’une utilisation en production, consultez les autres [articles sur la sécurité Azure](https://docs.microsoft.com/azure/security/).  

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> Vous pouvez être encore plus restrictif dans votre règle de pare-feu en [choisissant uniquement les adresses IP sortantes que votre application utilise](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

Dans Cloud Shell, exécutez à nouveau la commande pour autoriser l’accès à la base de données à partir de votre ordinateur local en remplaçant *\<you_ip_address>* par [votre adresse IP IPv4 locale](https://whatismyipaddress.com/). 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Connecter une application Python à la base de données de production

À cette étape, vous connectez votre exemple d’application Flask au serveur Azure Database pour PostgreSQL que vous avez créé.

### <a name="create-empty-database-and-user-access"></a>Créer une base de données vide et un accès utilisateur

Dans Cloud Shell, connectez-vous à la base de données en exécutant `psql`. Lorsque vous êtes invité à saisir votre mot de passe administrateur, utilisez celui que vous avez spécifié pour la [création d’un serveur Azure Database pour PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Créez la base de données et l’utilisateur à partir de l’interface de ligne de commande de PostgreSQL.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

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

![Application Python Flask s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-app.png)

## <a name="upload-app-to-a-container-registry"></a>Charger l’application dans un registre de conteneurs

À cette étape, vous créez une image Docker et la chargez dans Azure Container Registry. Vous pouvez également utiliser des registres populaires, tels que le hub Docker.

### <a name="build-the-docker-image-and-test-it"></a>Générer l’image Docker et la tester

Dans la fenêtre de terminal locale, générez l’image Docker.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker affiche une confirmation après la création de l’image.

```bash
Successfully built 7548f983a36b
```

Dans la racine du référentiel, ajoutez un fichier de variable d’environnement nommé _db.env_, puis ajoutez-y les variables d’environnement de base de données suivantes. L’application se connecte à la base de données de production Azure Database pour PostgreSQL.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Exécutez l’image localement dans un conteneur Docker. La commande suivante spécifie le fichier de variables d’environnement et mappe le port Flask par défaut 5000 au port local 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

La sortie est similaire à ce que vous avez vu plus haut. Toutefois, la migration de la base de données initiale n’a plus besoin d’être effectuée et elle est donc ignorée.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

La base de données contient déjà l’inscription que vous avez créée précédemment.

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-docker.png)

Maintenant que vous avez vérifié que le conteneur s’exécute localement, supprimez _db.env_. Dans Azure App Service, vous allez utiliser les paramètres d’application pour définir les variables d’environnement.  

### <a name="create-an-azure-container-registry"></a>Création d’un Azure Container Registry

Dans Cloud Shell, créez un registre dans Azure Container Registry avec la commande suivante. Remplacez *\<registry_name>* par un nom de registre unique.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>Récupérer les informations d’identification du registre

Dans Cloud Shell, exécutez les commandes suivantes pour récupérer les informations d’identification du registre. Vous en avez besoin pour envoyer et extraire les images.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Dans la sortie, vous voyez deux mots de passe. Notez le nom d’utilisateur (qui est le nom de registre par défaut) et le premier mot de passe.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-docker-image-to-registry"></a>Charger l’image Docker dans le registre

À partir de la fenêtre de terminal locale, connectez-vous à votre nouveau registre avec `docker`. Lorsque vous y êtes invité, renseignez le mot de passe que vous venez de récupérer.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Envoyez votre image Docker vers le registre.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>Créer l’application web avec l’image chargée

À cette étape, vous créez une application dans Azure App Service et la configurez pour utiliser l’image Docker chargée dans Azure Container Registry.

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web

Dans Cloud Shell, créez une application web dans le plan App Service *myAppServicePlan* avec la commande [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create).

Dans la commande suivante, remplacez l’espace réservé *\<app_name>* par un nom d’application unique. Ce nom est utilisé dans l’URL par défaut de l’application web. C’est pourquoi il doit être unique parmi l’ensemble des applications dans Azure App Service.

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

Une fois l’application web créée, Azure CLI affiche des informations similaires à celles de l’exemple suivant :

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Plus haut dans ce didacticiel, vous avez défini des variables d’environnement pour vous connecter à votre base de données PostgreSQL.

Dans App Service, vous définissez les variables d’environnement en tant que _paramètres d’application_ à l’aide de la commande [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

L’exemple suivant spécifie les informations de connexion à la base de données comme des paramètres d’application. Il utilise également la variable *WEBSITES_PORT* pour le port de conteneur 5000, qui permet au conteneur de recevoir le trafic HTTP sur le port 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>Configurer un déploiement personnalisé de conteneur

Même si vous déjà indiqué le nom de l’image de conteneur, vous devez encore spécifier l’URL du registre personnalisée et les informations d’identification de l’utilisateur. Dans Cloud Shell, exécutez la commande [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set).

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Dans Cloud Shell, redémarrez l’application. Le redémarrage garantit que tous les paramètres sont appliqués et que le dernier conteneur est extrait du registre.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Rechercher l’application web Azure 

Accédez à l’application web déployée. 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> L’application web met un certain temps à démarrer, car le conteneur doit être téléchargé et exécuté lors de la première sollicitation de l’application. Si vous voyez une erreur après un certain temps, actualisez simplement la page.

Vous verrez des invités déjà inscrits, qui ont été enregistrés dans la base de données de production Azure à l’étape précédente.

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Félicitations !** Vous exécutez une application Python dans Web App pour conteneurs.

## <a name="update-data-model-and-redeploy"></a>Mettre à jour le modèle de données et redéployer

Dans cette étape, vous ajoutez le nombre de participants à chaque inscription d’événement en mettant à jour le modèle `Guest`.

Dans la fenêtre de terminal locale, vérifiez la version *0.2-migration* avec la commande git suivante :

```bash
git checkout tags/0.2-migration
```

Cette version a déjà apporté les modifications nécessaires aux vues, aux contrôleurs et au modèle. Elle inclut également une migration de base de données générée via *alembic* (`flask db migrate`). Vous pouvez voir toutes les modifications apportées, via la commande git suivante :

```bash
git diff 0.1-initialapp 0.2-migration
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

![Application Python Flask basée sur un conteneur Docker s’exécutant localement](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publier les modifications dans Azure

Dans la fenêtre de terminal locale, générez la nouvelle image Docker et envoyez-la vers votre registre.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

Dans Cloud Shell, redémarrez l’application pour vous assurer que le dernier conteneur est extrait à partir du registre.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

Accédez à votre application web Azure et essayez de nouveau la nouvelle fonctionnalité. Créez un autre enregistrement d’événements.

```bash 
http://<app_name>.azurewebsites.net 
```

![Application Docker Python Flask dans Azure App Service](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Gérer votre application web Azure

Accédez au [portail Azure](https://portal.azure.com) pour voir l’application web que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis cliquez sur le nom de votre application web Azure.

![Navigation au sein du portail pour accéder à l’application web Azure](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application web. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application web.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à des applications web Azure](../app-service-web-tutorial-custom-domain.md)
