---
title: Créer une application Python avec PostgresSQL sur Linux - Azure App Service | Microsoft Docs
description: Découvrez comment exécuter une application Python orientée données dans Azure avec une connexion à une base de données PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: 00fc92ebe8b43f16791adce1f1cb9a1d6da7fbde
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534138"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Créer une application Python et PostgreSQL dans Azure App Service

[App Service sur Linux](app-service-linux-intro.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce didacticiel montre comment créer une application Python orientée données, à l’aide de PostgreSQL en tant que le serveur principal de base de données. Cela fait, vous disposez d’une application Django s’exécutant dans App Service sur Linux.

![Application Python Django dans App Service sur Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connecter une application Python à PostgreSQL
> * Déploiement de l’application dans Azure
> * Afficher les journaux de diagnostic
> * Gérer l’application dans le portail Azure

> [!NOTE]
> Avant de créer une instance Azure Database pour PostgreSQL, vérifiez [quelle génération de calcul est disponible dans votre région](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Vous pouvez suivre les étapes de cet article sur MacOS. Les instructions pour Linux et Windows sont identiques dans la plupart des cas, mais les différences ne sont pas détaillées dans ce didacticiel.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel :

1. [Installez Git](https://git-scm.com/)
2. [Installez Python](https://www.python.org/downloads/)
3. [Téléchargez et exécutez PostgreSQL](https://www.postgresql.org/download/)

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

Créez une base de données appelée *pollsdb* et configurez un utilisateur de base de données distinct, nommé *manager*, avec le mot de passe *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Créer une application Python locale

Cette étape consiste à configurer le projet Python Django local.

### <a name="clone-the-sample-app"></a>Clonage de l’exemple d’application

Ouvrez la fenêtre de terminal et entrez `CD` pour accéder à un répertoire de travail.

Exécutez les commandes suivantes pour cloner l’exemple de référentiel.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Cet exemple de dépôt contient une application [Django](https://www.djangoproject.com/). Il s’agit de la même application pilotée par les données que celle que vous obtiendriez en suivant le [tutoriel de prise en main dans la documentation Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Ce tutoriel ne vous enseigne pas Django, mais il vous montre comment obtenir, déployer et exécuter une application Django (ou une autre application Python basée sur des données) dans App Service.

### <a name="configure-environment"></a>Configurer l’environnement

Créez un environnement virtuel Python et utilisez un script pour définir les paramètres de connexion à la base de données.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

Les variables d’environnement définies dans *env.sh* et *env.ps1* sont utilisées dans _azuresite/settings.py_ pour définir les paramètres de la base de données.

### <a name="run-app-locally"></a>Exécuter l’application localement

Installez les packages demandés, [exécutez les migrations Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) et [créez un utilisateur administrateur](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

Dès que l’utilisateur administrateur est créé, exécutez le serveur Django.

```bash
python manage.py runserver
```

Lorsque l’application est entièrement chargée, vous obtenez un message similaire à celui-ci :

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Dans un navigateur, accédez à `http://localhost:8000`. Vous devriez voir le message `No polls are available.`. 

Accédez à `http://localhost:8000/admin` et connectez-vous à l’aide de l’utilisateur administrateur que vous avez créé à la dernière étape. Cliquez sur **Add** (Ajouter) en regard de **Questions** et créez une question de sondage à choix multiple.

![Application Python Django s’exécutant localement](./media/tutorial-python-postgresql-app/django-admin-local.png)

Accédez à `http://localhost:8000` de nouveau et consultez la question de sondage affichée.

L’exemple d’application Django stocke les données utilisateur dans la base de données. Si vous parvenez à ajouter une question de sondage, votre application écrit les données dans la base de données PostgreSQL locale.

À tout moment, pour arrêter le serveur Django, tapez Ctrl+C dans le terminal.

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
> Ce paramètre autorise les connexions de réseau à partir de toutes les adresses IP au sein du réseau Azure. À des fins de production, essayez de configurer les règles de pare-feu les plus restrictives possibles en [utilisant uniquement les adresses IP sortantes utilisées par votre application](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Dans Cloud Shell, exécutez à nouveau la commande pour autoriser l’accès à partir de votre ordinateur local en remplaçant *\<your_ip_address>* par [votre adresse IP IPv4 locale](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Connecter une application Python à la base de données de production

À cette étape, vous connectez votre exemple d’application Django au serveur Azure Database pour PostgreSQL, que vous avez créé.

### <a name="create-empty-database-and-user-access"></a>Créer une base de données vide et un accès utilisateur

Dans Cloud Shell, connectez-vous à la base de données en exécutant la commande ci-dessous. Lorsque vous êtes invité à saisir votre mot de passe administrateur, utilisez celui que vous avez spécifié pour la [création d’un serveur Azure Database pour PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Comme dans votre serveur Postgres local, créez la base de données et l’utilisateur dans le serveur Postgres Azure.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

> [!NOTE]
> Il est recommandé de créer des utilisateurs de base de données avec des autorisations restreintes pour des applications spécifiques, au lieu de faire appel à l’utilisateur administrateur. Dans cet exemple, l’utilisateur `manager` dispose de privilèges complets _uniquement_ pour la base de données `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>Tester la connectivité de l’application à base de données de production

Dans la fenêtre de terminal locale, modifiez les variables d’environnement de la base de données (que vous avez configurées précédemment en exécutant *env.sh* ou *env.ps1*) :

```bash
# Bash
export DBHOST="<postgresql_name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql_name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql_name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql_name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Exécutez la migration Django vers la base de données Azure et créez un utilisateur administrateur.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Dès que l’utilisateur administrateur est créé, exécutez le serveur Django.

```bash
python manage.py runserver
```

Accédez à `http://localhost:8000` de nouveau. Vous devriez voir le message `No polls are available.` de nouveau. 

Accédez à `http://localhost:8000/admin` et connectez-vous à l’aide de l’utilisateur administrateur que vous avez créé, puis créez une autre question de sondage, comme avant.

![Application Python Django s’exécutant localement](./media/tutorial-python-postgresql-app/django-admin-local.png)

Accédez à `http://localhost:8000` de nouveau et consultez la question de sondage affichée. Votre application écrit désormais les données dans la base de données, dans Azure.

## <a name="deploy-to-azure"></a>Déployer dans Azure

Dans cette étape, vous allez déployer l’application Python connectée à Postgres dans Azure App Service.

### <a name="configure-repository"></a>Configurer le référentiel

Django valide l’en-tête `HTTP_HOST` dans les requêtes entrantes. Pour que votre application Django fonctionne dans App Service, vous devez ajouter le nom de domaine complet de l’application aux hôtes autorisés. Ouvrez _azuresite/settings.py_ et recherchez le paramètre `ALLOWED_HOSTS`. Remplacez la ligne par :

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Comme Django ne prend pas en charge le [traitement des fichiers statiques en production](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), vous devez ensuite activer cette opération manuellement. Pour ce tutoriel, vous utilisez [WhiteNoise](https://whitenoise.evans.io/en/stable/). Le package WhiteNoise est déjà inclus dans _requirements.txt_. Vous devez simplement configurer Django pour qu’il l’utilise. 

Dans _azuresite/settings.py_, recherchez le paramètre `MIDDLEWARE` et ajoutez le middleware `whitenoise.middleware.WhiteNoiseMiddleware` à la liste, juste en dessous du middleware `django.middleware.security.SecurityMiddleware`. Votre paramètre `MIDDLEWARE` doit avoir l’aspect suivant :

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

À la fin de _azuresite/settings.py_, ajoutez les lignes suivantes.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Pour plus d’informations sur la configuration de WhiteNoise, consultez la [documentation WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> La section sur les paramètres de la base de données respecte déjà les bonnes pratiques de sécurité pour l’utilisation de variables d’environnement. Pour les suggestions de déploiement complet, consultez [Documentation Django : Liste de vérifications pour le déploiement](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).


Validez vos modifications dans le dépôt.

```bash
git commit -am "configure for App Service"
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
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
   06b6df4..6520eea  master -> master
```  

Le serveur de déploiement App Service voit _requirements.txt_ dans la racine du dépôt et exécute automatiquement la gestion des packages Python après `git push`.

### <a name="browse-to-the-azure-app"></a>Accéder à l’application Azure

Accédez à l’application déployée. L’application web met un certain temps à démarrer, car le conteneur doit être téléchargé et exécuté lors de la première sollicitation de l’application. Si la page expire ou affiche un message d’erreur, patientez quelques minutes et actualisez la page.

```bash
http://<app_name>.azurewebsites.net
```

Vous devez voir la question de sondage que vous avez créée précédemment. 

App Service détecte un projet Django dans votre dépôt en recherchant dans chaque sous-répertoire un fichier _wsgi.py_ qui est créé par `manage.py startproject` par défaut. Lorsqu’il trouve le fichier, il charge l’application Django. Pour obtenir plus d’informations sur la façon dont App Service charge les applications Python, consultez [Configurer une image Python intégrée](how-to-configure-python.md).

Accédez à `<app_name>.azurewebsites.net` et connectez-vous à l’aide de l’utilisateur administrateur que vous avez créé. Si vous le souhaitez, essayez de créer d’autres questions de sondage.

![Application Python Django s’exécutant localement](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Félicitations !** Vous exécutez une application Python dans App Service pour Linux.

## <a name="access-diagnostic-logs"></a>Accéder aux journaux de diagnostic

Dans App Service sur Linux, les applications sont exécutées dans un conteneur d’une image Docker par défaut. Vous pouvez accéder aux journaux de console générés à partir du conteneur. Pour obtenir ces journaux, activez tout d’abord la journalisation du conteneur en exécutant la commande suivante dans Cloud Shell :

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Une fois la journalisation du conteneur activée, exécutez la commande suivante pour voir le flux de journal :

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

## <a name="manage-your-app-in-the-azure-portal"></a>Gérer votre application dans le Portail Azure

Accédez au [Portail Azure](https://portal.azure.com) pour voir l’application que vous avez créée.

Dans le menu de gauche, cliquez sur **App Services**, puis sur le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/tutorial-python-postgresql-app/app-resource.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application. Cette page propose un aperçu de votre application. Ici, vous pouvez également effectuer des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple). Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Page App Service du Portail Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer une base de données PostgreSQL dans Azure
> * Connecter une application Python à PostgreSQL
> * Déploiement de l’application dans Azure
> * Afficher les journaux de diagnostic
> * Gérer l’application dans le portail Azure

Passez au didacticiel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application.

> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à Azure App Service](../app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Configurer l’image Python intégrée et corriger les erreurs](how-to-configure-python.md)

