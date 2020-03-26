---
title: 'Tutoriel : Application Linux Python avec Postgre'
description: Découvrez comment faire fonctionner une application Linux Python dans Azure App Service en établissant une connexion à une base de données PostgreSQL dans Azure. Django est utilisé dans ce tutoriel.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523929"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutoriel : Exécuter une application web Python (Django) avec PostgreSQL dans Azure App Service

[Azure App Service](app-service-linux-intro.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce tutoriel montre comment connecter une application Web Python Django pilotée par les données à une base de données Azure Database pour PostgreSQL, et comment déployer et exécuter l’application sur Azure App Service.

![Application web Python Django dans Azure App Service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une base de données Azure Database pour PostgreSQL et y connecter une application web.
> * Déployer l’application web sur Azure App Service.
> * Afficher les journaux de diagnostic
> * Gérer l’application web dans le portail Azure.

Vous pouvez suivre les étapes de cet article sur macOS, Linux ou Windows. Les étapes sont similaires dans la plupart des cas, bien que les différences ne soient pas détaillées dans ce tutoriel. La plupart des exemples ci-dessous utilisent une fenêtre de terminal `bash` sur Linux. 

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installez [Git](https://git-scm.com/).
- Installez [Python 3](https://www.python.org/downloads/).
- Installez et exécutez [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Tester l’installation PostgreSQL et créer une base de données

Tout d’abord, connectez-vous à votre serveur PostgreSQL local et créez une base de données : 

Dans une fenêtre de terminal locale, exécutez `psql` pour vous connecter à votre serveur PostgreSQL local en tant qu’utilisateur intégré `postgres`.

```bash
sudo su - postgres
psql
```
or
```PowerShell
psql -U postgres
```

Si la connexion est établie, cela signifie que votre base de données PostgreSQL est en cours d’exécution. Dans le cas contraire, assurez-vous que votre base de données PostgresQL est démarrée en suivant les étapes relatives à votre système d’exploitation dans la page [Téléchargements - Distribution principale de PostgreSQL](https://www.postgresql.org/download/).

Créez une base de données appelée *pollsdb* et configurez un utilisateur de base de données nommé *manager*, avec le mot de passe *supersecretpass* :

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Saisissez `\q` pour quitter le client PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Créer et exécuter l’application Python locale

Ensuite, configurez et exécutez l’exemple d’application web Python Django.

L’exemple de dépôt [djangoapp](https://github.com/Azure-Samples/djangoapp) contient l’application de sondage [Django](https://www.djangoproject.com/) pilotée par les données que vous obtenez en suivant les étapes de l’article sur l’[écriture de votre première application Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) dans la documentation Django.

### <a name="clone-the-sample-app"></a>Clonage de l’exemple d’application

Dans une fenêtre de terminal, exécutez les commandes suivantes pour cloner le dépôt de l’exemple d’application, puis accédez au nouveau répertoire de travail :

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Configurer l’environnement virtuel Python

Créez et activez un environnement virtuel Python pour exécuter votre application.

```bash
python3 -m venv venv
source venv/bin/activate
```
or
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

Dans l’environnement `venv`, exécutez *env.sh* ou *env.ps1* pour définir les variables d’environnement que *azuresite/settings.py* utilisera pour les paramètres de connexion à la base de données.

```bash
source ./env.sh
```
or
```PowerShell
.\env.ps1
```

Installez les packages requis indiqués dans *requirements.txt*, exécutez les [migrations Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) et [créez un utilisateur administrateur](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user) :

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Exécuter l’application web

Après avoir créé l’utilisateur administrateur, exécutez le serveur Django.

```bash
python manage.py runserver
```

Quand l’application web Django est entièrement chargée, elle retourne un message similaire à celui-ci :

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Accédez à *http:\//localhost:8000* dans un navigateur. Vous devez voir le message **No polls are available** (Aucun sondage n’est disponible). 

Accédez à *http:\//localhost:8000/admin*, puis connectez-vous à l’aide de l’utilisateur administrateur que vous avez créé à la dernière étape. Sélectionnez **Add** (Ajouter) en regard de **Questions** et créez une question de sondage à choix multiple.

![Exécuter l’application Python Django dans App Services localement](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Accédez à nouveau à *http:\//localhost:8000* pour voir la question de sondage et y répondre. L’exemple d’application Django local écrit et stocke les données utilisateur dans la base de données PostgreSQL locale.

Pour arrêter le serveur Django, tapez Ctrl+C dans le terminal.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

La plupart des étapes restantes de cet article utilisent des commandes Azure CLI dans Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Créer et se connecter à Azure Database pour PostgreSQL

Dans cette section, vous allez créer une base de données et un serveur Azure Database pour PostgreSQL, et y connecter votre application web. Quand vous déployez votre application web sur Azure App Service, elle utilise cette base de données cloud. 

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Vous pouvez créer un groupe de ressources pour votre serveur Azure Database pour PostgreSQL ou utiliser un groupe de ressources existant. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur Azure Database pour PostgreSQL

Créez un serveur PostgreSQL avec la commande [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) dans Cloud Shell.

> [!NOTE]
> Avant de créer un serveur Azure Database pour PostgreSQL, vérifiez quelle [génération de calcul](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) est disponible dans votre région. Si votre région ne prend pas en charge le matériel Gen4, remplacez *--sku-name* dans la ligne de commande suivante par une valeur prise en charge dans votre région, par exemple Gen5. 

Dans la commande suivante, remplacez *\<postgresql-name>* par un nom de serveur unique. Le nom de serveur fait partie de votre point de terminaison PostgreSQL *https://\<postgresql-name>.postgres.database.azure.com*, c’est pourquoi il doit être unique parmi l’ensemble des serveurs dans Azure. 

Remplacez *\<resourcegroup-name>* et *\<region>* par le nom et la région du groupe de ressources que vous souhaitez utiliser. Pour *\<admin-username>* et *\<admin-password>* , créez les informations d’identification de l’utilisateur pour le compte administrateur de la base de données. Mémorisez *\<admin-username>* et *\<admin-password>* , car vous devrez les utiliser ultérieurement pour vous connecter aux bases de données et au serveur PostgreSQL.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

Quand le serveur Azure Database pour PostgreSQL est créé, Azure CLI retourne du code JSON semblable à l’exemple suivant :

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Créer des règles de pare-feu pour le serveur Azure Database pour PostgreSQL

Exécutez la commande [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) pour autoriser l’accès à la base de données à partir des ressources Azure. Remplacez les espaces réservés *\<postgresql-name>* et *\<resourcegroup-name>* par vos valeurs.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Le paramètre précédent autorise les connexions réseau à partir de toutes les adresses IP au sein du réseau Azure. À des fins de production, essayez de configurer les règles de pare-feu les plus restrictives possibles en [autorisant uniquement les adresses IP sortantes utilisées par votre application](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Réexécutez la commande `firewall-rule create` pour autoriser l’accès à partir de votre ordinateur local. Remplacez *\<your-ip-address>* par [votre adresse IP IPv4 locale](https://www.whatsmyip.org/). Remplacez les espaces réservés *\<postgresql-name>* et *\<resourcegroup-name>* par vos propres valeurs.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Créer et se connecter à la base de données Azure Database pour PostgreSQL

Connectez-vous à votre serveur Azure Database pour PostgreSQL en exécutant la commande suivante. Utilisez vos propres *\<postgresql-name>* et *\<admin-username>* , et connectez-vous avec le mot de passe que vous avez créé.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Tout comme vous l’avez fait sur votre serveur PostgreSQL local, créez une base de données et un utilisateur sur le serveur Azure Database pour PostgreSQL :

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Nous vous recommandons de créer des utilisateurs de base de données avec des autorisations restreintes pour des applications spécifiques, au lieu de faire appel à l’utilisateur administrateur. L’utilisateur `manager` dispose de privilèges complets *uniquement* pour la base de données `pollsdb`.

Saisissez `\q` pour quitter le client PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Tester la connectivité de l’application à la base de données Azure PostgreSQL

Modifiez votre fichier local *env.sh* ou *env.ps1* pour qu’il pointe vers votre base de données cloud PostgreSQL en remplaçant *\<postgresql-name>* par le nom de votre serveur Azure Database pour PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
or
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Dans l’environnement `venv` dans votre fenêtre de terminal local, exécutez le fichier *env.sh* ou *env.ps1* modifié. 
```bash
source ./env.sh
```
or
```PowerShell
.\env.ps1
```

Exécutez la migration Django vers la base de données Azure, et créez un utilisateur administrateur.

```bash
python manage.py migrate
python manage.py createsuperuser
```

Dès que l’utilisateur administrateur est créé, exécutez le serveur Django.

```bash
python manage.py runserver
```

Dans un navigateur, accédez à *http:\//localhost:8000*. Vous devriez revoir le message **No polls are available**. 

Accédez à *http:\//localhost:8000/admin*, connectez-vous à l’aide de l’utilisateur administrateur que vous avez créé, puis créez une autre question de sondage comme précédemment.

![Exécuter l’application Python Django dans App Services localement](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Accédez à nouveau à *http:\//localhost:8000*, et observez que la question de sondage est affichée. Votre application écrit à présent des données dans la base de données Azure Database pour PostgreSQL.

Pour arrêter le serveur Django, tapez Ctrl+C dans le terminal.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Déployer l’application web sur Azure App Service.

Lors de cette étape, vous allez déployer l’application Python connectée à la base de données Azure Database pour PostgreSQL sur Azure App Service.

### <a name="configure-repository"></a>Configurer le référentiel

Étant donné que ce tutoriel utilise un exemple Django, vous devez modifier et ajouter certains paramètres dans votre fichier *djangoapp/azuresite/settings.py* pour qu’il fonctionne avec Azure App Service. 

1. Django valide l’en-tête `HTTP_HOST` dans les requêtes entrantes. Pour que votre application web Django fonctionne dans App Service, vous devez ajouter le nom de domaine complet de l’application aux hôtes autorisés. 
   
   Modifiez *azuresite/settings.py* afin de changer la ligne `ALLOWED_HOSTS` comme suit :
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django ne prend pas en charge le [traitement des fichiers statiques en production](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). Pour ce tutoriel, vous utilisez [WhiteNoise](https://whitenoise.evans.io/en/stable/) pour permettre le traitement des fichiers. Le package WhiteNoise a déjà été installé avec *requirements.txt*. 
   
   Pour configurer Django afin d’utiliser WhiteNoise, dans *azuresite/settings.py*, recherchez le paramètre `MIDDLEWARE` et ajoutez `whitenoise.middleware.WhiteNoiseMiddleware` à la liste, juste après la ligne `django.middleware.security.SecurityMiddleware`. Votre paramètre `MIDDLEWARE` doit avoir l’aspect suivant :
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. À la fin de *azuresite/settings.py*, ajoutez les lignes suivantes :
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Pour plus d’informations sur la configuration de WhiteNoise, consultez la [documentation WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> La section sur les paramètres de la base de données respecte déjà les bonnes pratiques de sécurité pour l’utilisation de variables d’environnement. Pour obtenir des suggestions de déploiement complet, consultez [Documentation Django : Liste de vérifications pour le déploiement](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Validez vos modifications dans votre fourche du dépôt *djangoapp* :

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Configuration d’un utilisateur de déploiement

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Créer un plan App Service

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Créer une application web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Plus haut dans ce didacticiel, vous avez défini des variables d’environnement pour vous connecter à votre base de données PostgreSQL.

Dans Azure App Service, vous définissez les variables d’environnement en tant que *paramètres d’application* à l’aide de la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

Dans Azure Cloud Shell, exécutez la commande suivante pour spécifier les détails de connexion à la base de données en tant que paramètres d’application. Remplacez les espaces réservés *\<app-name>* , *\<resourcegroup-name>* et *\<postgresql-name>* par vos propres valeurs.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Pour plus d’informations sur la façon dont votre code accède à ces paramètres d’application, consultez [Accéder aux variables d’environnement](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Effectuer une transmission de type push vers Azure à partir de Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

Le serveur de déploiement App Service voit *requirements.txt* dans la racine du dépôt et exécute automatiquement la gestion des packages Python après `git push`.

### <a name="browse-to-the-azure-app"></a>Accéder à l’application Azure

Accédez à l’application déployée avec l’URL *http:\//\<app-name>.azurewebsites.net*. L’application web met un certain temps à démarrer, car le conteneur doit être téléchargé et exécuté lors de la première sollicitation de l’application. Si la page expire ou affiche un message d’erreur, patientez quelques minutes et actualisez la page.

Vous devez voir les questions de sondage que vous avez créées. 

App Service détecte un projet Django dans votre dépôt en recherchant dans chaque sous-répertoire un fichier *wsgi.py*, que `manage.py startproject` crée par défaut. Quand App Service trouve le fichier, il charge l’application web Django. Pour obtenir plus d’informations sur la façon dont App Service charge les applications Python, consultez [Configurer une image Python intégrée](how-to-configure-python.md).

Accédez à *http:\//\<app-name>.azurewebsites.net/admin* et connectez-vous à l’aide de l’utilisateur administrateur que vous avez créé. Si vous le souhaitez, créez d’autres questions de sondage.

![Exécuter l’application Python Django dans App Services dans Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Félicitations !** Vous exécutez une application Python (Django) dans Azure App Service pour Linux.

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Gérer votre application dans le portail Azure

Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez l’application que vous avez créée

![Accéder à votre application Python Django dans le portail Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application. Cette page propose un aperçu de votre application. Elle vous permet d’exécuter des tâches de gestion de base telles que parcourir, arrêter, redémarrer et supprimer. Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Gérer votre application Python Django dans la page Vue d’ensemble du portail Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application :

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer une application Python](how-to-configure-python.md)
