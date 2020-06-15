---
title: 'Tutoriel : Déployer Python (Django) avec Postgres'
description: Découvrez comment créer une application Python avec une base de données PostgreSQL et la déployer sur Azure App Service sur Linux. Le tutoriel utilise un exemple d’application Django pour la démonstration.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- tracking-python
ms.openlocfilehash: 4a2f80ea30fc68ae1dfea72983fd2b229d40c711
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559281"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Tutoriel : Déployer une application web Python (Django) avec PostgreSQL dans Azure App Service

Ce tutoriel montre comment déployer une application web Python (Django) pilotée par les données dans [Azure App Service](app-service-linux-intro.md) et la connecter à une base de données Azure Database pour PostgreSQL. App Service offre un service d’hébergement hautement évolutif appliquant des mises à jour correctives automatiques.

![Déployer une application web Python Django sur Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une base de données Azure Database pour PostgreSQL
> * Déployer du code pour Azure App Service et se connecter à Postgres
> * Mettre à jour votre code et effectuer un redéploiement
> * Afficher les journaux de diagnostic
> * Gérer l’application web dans le portail Azure.

Vous pouvez suivre les étapes de cet article sur macOS, Linux ou Windows.

## <a name="install-dependencies"></a>Installer des dépendances

Avant de commencer ce tutoriel :

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installez [Azure CLI](/cli/azure/install-azure-cli).
- Installez [Git](https://git-scm.com/).
- Installez [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Clonage de l’exemple d’application

Dans une fenêtre de terminal, exécutez les commandes suivantes pour cloner l’exemple de dépôt d’application, puis accédez à la racine du dépôt :

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

L’exemple de dépôt djangoapp contient l’application de sondage [Django](https://www.djangoproject.com/) pilotée par les données que vous obtenez en suivant les étapes décrivant l’[écriture de votre première application Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) dans la documentation Django. Il est fourni ici pour vous simplifier la tâche.

## <a name="prepare-app-for-app-service"></a>Préparer une application pour App Service

Comme de nombreux frameworks web Python, Django [nécessite certains changements avant de pouvoir être exécuté sur un serveur de production](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/), et il en est de même pour App Service. Vous devez changer et ajouter des paramètres dans le fichier *azuresite/settings.py* par défaut afin que l’application fonctionne après son déploiement sur App Service. 

Examinez *azuresite/production.py*, qui produit la configuration nécessaire pour App Service. De manière schématique, il effectue les opérations suivantes :

- Hériter de tous les paramètres du fichier *azuresite/settings.py*.
- Ajouter le nom de domaine complet de l’application App Service aux hôtes autorisés. 
- Utilisez [WhiteNoise](https://whitenoise.evans.io/en/stable/) pour activer le traitement des fichiers statiques en production, car Django ne traite pas par défaut les fichiers statiques en production. Le package WhiteNoise est déjà inclus dans *requirements.txt*.
- Ajoutez une configuration pour une base de données PostgreSQL. Par défaut, Django utilise Sqlite3 comme base de données, mais il n’est pas approprié pour les applications de production. Le package [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) est déjà inclus dans *requirements.txt*.
- La configuration Postgres utilise des variables d’environnement. Plus tard, vous découvrirez comment définir des variables d’environnement dans App Service.

Pour des raisons pratiques, *azuresite/production.py* est inclus dans le dépôt, mais il n’est pas encore utilisé par l’application. Pour vérifier que ses paramètres sont utilisés dans App Service, vous devez configurer deux fichiers, *manage.py* et *azuresite/wsgi.py* afin d’y accéder.

- Dans *manage.py*, remplacez la ligne suivante :

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Par le code suivant :

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Vous définirez la variable d’environnement `DJANGO_ENV` ultérieurement, quand vous configurerez votre application App Service.

- Dans *azuresite/wsgi.py*, effectuez le même changement que ci-dessus.

    Dans App Service, vous utilisez *manage.py* pour exécuter des migrations de base de données, et App Service utilise *azuresite/wsgi.py* pour exécuter votre application Django en production. Ce changement apporté dans les deux fichiers garantit que les paramètres de production sont utilisés dans les deux cas.

## <a name="sign-in-to-azure-cli"></a>Connectez-vous à Azure CLI

Azure CLI doit déjà être installé. [Azure CLI](/cli/azure/what-is-azure-cli) vous permet d’utiliser des ressources Azure à partir du terminal de ligne de commande. 

Pour vous connecter à Azure, exécutez la commande [`az login`](/cli/azure/reference-index#az-login) :

```azurecli
az login
```

Suivez les instructions affichées dans le terminal pour vous connecter à votre compte Azure. Quand vous avez terminé, vos abonnements sont affichés au format JSON dans la sortie du terminal.

## <a name="create-postgres-database-in-azure"></a>Créer une base de données Postgres dans Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

Dans cette section, vous allez créer un serveur et une base de données Azure Database pour PostgreSQL. Pour commencer, installez l’extension, `db-up` avec la commande suivante :

```azurecli
az extension add --name db-up
```

Créez la base de données Postgres dans Azure avec la commande [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), comme indiqué dans l’exemple suivant. Remplacez *\<postgresql-name>* par un nom *unique* (le point de terminaison de serveur est *https://\<postgresql-name>.postgres.database.azure.com*). Pour *\<admin-username>* et *\<admin-password>* , spécifiez les informations d’identification afin de créer un utilisateur administrateur pour ce serveur Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Cette commande peut prendre un certain temps, car elle effectue les opérations suivantes :

- Crée un [groupe de ressources](../../azure-resource-manager/management/overview.md#terminology) appelé `myResourceGroup`, s’il n’existe pas. Chaque ressource Azure doit se trouver dans un groupe de ce type. `--resource-group` est facultatif.
- Crée un serveur Postgres avec l’utilisateur administratif.
- Crée une base de données `pollsdb`.
- Autorise l’accès à partir de votre adresse IP locale.
- Autorise l’accès à partir des services Azure.
- Créez un utilisateur de base de données avec un accès à la base de données `pollsdb`.

Vous pouvez effectuer toutes les étapes séparément avec d’autres commandes `az postgres` et `psql`, mais `az postgres up` les effectue toutes en fait une seule opération pour vous.

Une fois la commande terminée, recherchez les lignes de sortie qui commencent par `Ran Database Query:`. Elles affichent l’utilisateur de base de données créé pour vous, avec le nom d’utilisateur `root` et le mot de passe `Pollsdb1`. Vous les utiliserez ultérieurement pour connecter votre application à la base de données.

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>` peut être défini sur n’importe quelle [région Azure](https://azure.microsoft.com/global-infrastructure/regions/). Vous pouvez récupérer les régions disponibles pour votre abonnement à l’aide de la commande [`az account list-locations`](/cli/azure/account#az-account-list-locations). Pour les applications de production, placez votre base de données et votre application dans le même emplacement.

## <a name="deploy-the-app-service-app"></a>Déployer l’application App Service

Dans cette section, vous allez créer l’application App Service. Vous allez connecter cette application à la base de données Postgres que vous avez créée et déployer votre code.

### <a name="create-the-app-service-app"></a>Créer l’application App Service

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Vérifiez que vous êtes à nouveau à la racine du dépôt (`djangoapp`), car l’application sera déployée à partir de ce répertoire.

Créez une application App Service avec la commande [`az webapp up`](/cli/azure/webapp#az-webapp-up), comme indiqué dans l’exemple suivant. Remplacez *\<app-name>* par un nom *unique* (le point de terminaison de serveur est *https://\<app-name>.azurewebsites.net*). Les caractères autorisés pour *\<app-name>* sont `A`-`Z`, `0`-`9` et `-`.

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Cette commande peut prendre un certain temps, car elle effectue les opérations suivantes :

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Génère automatiquement un [groupe de ressources](../../azure-resource-manager/management/overview.md#terminology).
- Crée le [plan App Service](../overview-hosting-plans.md) *myAppServicePlan* dans le niveau tarifaire De base (B1), le cas échéant. Les valeurs `--plan` et `--sku` sont facultatives.
- Crée l’application App Service si elle n’existe pas.
- Active la journalisation par défaut pour l’application, si elle ne l’est pas déjà.
- Charge le dépôt à l’aide du déploiement ZIP avec l’automatisation de la génération activée.

Une fois le déploiement terminé, vous voyez une sortie JSON semblable à ce qui suit :

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Copiez la valeur de *\<app-resource-group>* . Vous en aurez besoin pour configurer l’application ultérieurement. 

> [!TIP]
> Les paramètres pertinents sont enregistrés dans un répertoire caché *.azure* de votre référentiel. Vous pouvez utiliser la même commande simple plus tard pour redéployer tous les changements et activer immédiatement les journaux de diagnostic avec :
> 
> ```azurecli
> az webapp up
> ```

L’exemple de code est maintenant déployé, mais l’application ne se connecte pas encore à la base de données Postgres dans Azure. Ce sera votre prochaine tâche.

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Quand vous exécutez votre application localement, vous pouvez définir les variables d’environnement dans la session de terminal. Dans App Service, vous effectuez cette opération avec des *paramètres d’application*, à l’aide de la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Exécutez la commande suivante pour spécifier les informations de connexion à la base de données sous forme de paramètres d’application. Remplacez *\<app-name>* , *\<app-resource-group>* et *\<postgresql-name>* par vos propres valeurs. N’oubliez pas que les informations d’identification de l’utilisateur `root` et `Pollsdb1` ont été automatiquement créées par la commande `az postgres up`.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Pour plus d’informations sur la façon dont votre code accède à ces paramètres d’application, consultez [Accéder aux variables d’environnement](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Exécuter des migrations de base de données

Pour exécuter des migrations de base de données dans App Service, ouvrez une session SSH dans le navigateur en accédant à *https://\<app-name>.scm.azurewebsites.net/webssh/host* :

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Dans la session SSH, exécutez les commandes suivantes :

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Accéder à l’application Azure

Accédez à l’application déployée avec l’URL *http:\//\<app-name>.azurewebsites.net* dans un navigateur. Vous devez voir le message **No polls are available** (Aucun sondage n’est disponible). 

Accédez à *http:\//\<app-name>.azurewebsites.net/admin*, puis connectez-vous avec l’utilisateur administrateur que vous avez créé à la dernière étape. Sélectionnez **Add** (Ajouter) en regard de **Questions** et créez une question de sondage à choix multiple.

Accédez à l’application déployée avec l’URL *http:\//\<app-name>.azurewebsites.net/admin*, puis créez des questions de sondage. Vous pouvez voir les questions sur *http:\//\<app-name>.azurewebsites.net/* . 

![Exécuter l’application Python Django dans App Services dans Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Accédez à nouveau à l’application déployée avec l’URL *http:\//\<app-name>.azurewebsites.net* pour voir la question de sondage et y répondre.

App Service détecte un projet Django dans votre dépôt en recherchant dans chaque sous-répertoire un fichier *wsgi.py*, que `manage.py startproject` crée par défaut. Quand App Service trouve le fichier, il charge l’application web Django. Pour obtenir plus d’informations sur la façon dont App Service charge les applications Python, consultez [Configurer une image Python intégrée](how-to-configure-python.md).

**Félicitations !** Vous exécutez une application Python (Django) dans Azure App Service pour Linux.

## <a name="develop-app-locally-and-redeploy"></a>Développer localement une application et effectuer un redéploiement

Dans cette section, vous allez développer votre application dans votre environnement local et redéployer votre code sur App Service.

### <a name="set-up-locally-and-run"></a>Configurer localement et exécuter

Pour configurer votre environnement de développement local et exécuter l’exemple d’application pour la première fois, exécutez les commandes suivantes :

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

Quand l’application web Django est entièrement chargée, elle retourne un message similaire à celui-ci :

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Accédez à *http:\//localhost:8000* dans un navigateur. Vous devez voir le message **No polls are available** (Aucun sondage n’est disponible). 

Accédez à *http:\//localhost:8000/admin*, puis connectez-vous à l’aide de l’utilisateur administrateur que vous avez créé à la dernière étape. Sélectionnez **Add** (Ajouter) en regard de **Questions** et créez une question de sondage à choix multiple.

![Exécuter l’application Python Django dans App Services localement](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Accédez à nouveau à *http:\//localhost:8000* pour voir la question de sondage et y répondre. L’exemple d’application Django locale écrit et stocke les données utilisateur dans une base de données Sqlite3 locale. Vous n’avez donc pas à vous soucier de la désorganisation de votre base de données de production. Pour faire correspondre votre environnement de développement à l’environnement Azure, envisagez de plutôt utiliser une base de données Postgres localement.

Pour arrêter le serveur Django, tapez Ctrl+C.

### <a name="update-the-app"></a>Mettre à jour l’application

Juste pour voir comment fonctionnent les mises à jour d’application, apportez un petit changement dans `polls/models.py`. Recherchez la ligne :

<pre>
choice_text = models.CharField(max_length=200)
</pre>

Et remplacez-la par :

```python
choice_text = models.CharField(max_length=100)
```

Le changement du modèle de données implique la création d’une migration Django. Pour ce faire, utilisez la commande suivante :

```
python manage.py makemigrations
```

Vous pouvez tester vos changements localement en exécutant des migrations, en exécutant le serveur de développement et en accédant à *http:\//localhost:8000/admin* :

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Redéployer du code dans Azure

Pour redéployer les changements, exécutez la commande suivante à partir de la racine du dépôt :

```azurecli
az webapp up
```

App Service détecte que l’application existe et déploie simplement le code.

### <a name="rerun-migrations-in-azure"></a>Réexécuter des migrations dans Azure

Étant donné que vous avez apporté des changements au modèle de données, vous devez réexécuter les migrations de base de données dans App Service. Ouvrez une session SSH dans le navigateur en accédant à *https://\<app-name>.scm.azurewebsites.net/webssh/host*. Exécutez les commandes suivantes :

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Examiner une application en production

Accédez à *http:\//\<app-name>.azurewebsites.net*, puis consultez les changements s’exécutant en temps réel en production. 

## <a name="stream-diagnostic-logs"></a>Diffuser les journaux de diagnostic

Vous pouvez accéder aux journaux de la console générés à partir du conteneur.

> [!TIP]
> `az webapp up` active automatiquement la journalisation par défaut. Pour des raisons de performances, cette journalisation se désactive après un certain temps, mais se réactive chaque fois que vous réexécutez `az webapp up`. Pour l’activer manuellement, exécutez la commande suivante :
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Exécutez la commande Azure CLI suivante dans le flux de journaux :

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Si vous ne voyez pas les journaux d’activité de la console, attendez 30 secondes et vérifiez à nouveau.

> [!NOTE]
> Vous pouvez également inspecter les fichiers journaux à partir du navigateur sur `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Pour arrêter le streaming des journaux à tout moment, appuyez sur `Ctrl`+`C`.

## <a name="manage-your-app-in-the-azure-portal"></a>Gérer votre application dans le portail Azure

Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez l’application que vous avez créée

![Accéder à votre application Python Django dans le portail Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Par défaut, le portail affiche la page **Vue d’ensemble** de votre application. Cette page propose un aperçu de votre application. Elle vous permet d’exécuter des tâches de gestion de base telles que parcourir, arrêter, redémarrer et supprimer. Les onglets sur le côté gauche de la page affichent les différentes pages de configuration que vous pouvez ouvrir.

![Gérer votre application Python Django dans la page Vue d’ensemble du portail Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, supprimez les groupes de ressources en exécutant les commandes suivantes :

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une base de données Azure Database pour PostgreSQL
> * Déployer du code pour Azure App Service et se connecter à Postgres
> * Mettre à jour votre code et effectuer un redéploiement
> * Afficher les journaux de diagnostic
> * Gérer l’application web dans le portail Azure.

Passez au tutoriel suivant pour découvrir comment mapper un nom DNS personnalisé à votre application :

> [!div class="nextstepaction"]
> [Tutoriel : Mappage d’un nom DNS personnalisé à une application](../app-service-web-tutorial-custom-domain.md)

Ou consultez les autres ressources :

> [!div class="nextstepaction"]
> [Configurer une application Python](how-to-configure-python.md)
