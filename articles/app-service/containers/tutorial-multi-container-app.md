---
title: Créer une application multiconteneur (préversion) dans Web App pour conteneurs
description: Découvrez comment utiliser plusieurs conteneurs sur Azure avec des fichiers de configuration Docker Compose et Kubernetes, avec une application WordPress et MySQL.
keywords: service d’application Azure, application web, Linux, Docker, Compose, multiconteneur, plusieurs conteneurs, Web App pour conteneurs, plusieurs conteneurs, conteneurs, Kubernetes, Wordpress, base de données Azure pour MySQL, base de données de production avec des conteneurs
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: dcda4e25932a74313674e91afc7382ea19724613
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129948"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Tutoriel : Création d’une application multiconteneur (préversion) dans Web App pour conteneurs

[Web App pour conteneurs](app-service-linux-intro.md) fournit une solution souple d’utilisation des images Docker. Dans ce tutoriel, vous allez apprendre à créer une application à plusieurs conteneurs à l’aide de WordPress et de MySQL. Vous allez effectuer ce tutoriel dans Cloud Shell, mais vous pouvez également exécuter ces commandes localement avec [Cloud Shell](/cli/azure/install-azure-cli) (2.0.32 ou ultérieur).

Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Convertir une configuration Docker Compose pour travailler avec Web App pour conteneurs
> * Convertir une configuration Kubernetes pour travailler avec Web App pour conteneurs
> * Déployer une application à plusieurs conteneurs vers Azure
> * Ajouter des paramètres d’application
> * Utiliser le stockage persistant pour vos conteneurs
> * Se connecter à la base de données Azure pour MySQL
> * Résoudre les erreurs

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez connaître [Docker Compose](https://docs.docker.com/compose/) ou [Kubernetes](https://kubernetes.io/).

## <a name="download-the-sample"></a>Téléchargez l’exemple

Pour ce didacticiel, vous utilisez le fichier de composition de [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), mais vous le modifiez pour inclure Azure Database pour MySQL, le stockage persistant et Redis. Le fichier de configuration se trouve dans les [exemples Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Dans Cloud Shell, créez un répertoire pour le tutoriel, puis accédez à ce répertoire.

```bash
mkdir tutorial

cd tutorial
```

Exécutez ensuite la commande suivante pour cloner le dépôt de l’exemple d’application dans le répertoire de votre tutoriel. Accédez ensuite au répertoire `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Dans Cloud Shell, créez un groupe de ressources avec la commande [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *USA Centre Sud*. Pour afficher tous les emplacements pris en charge pour App Service sous Linux au niveau **Standard**, exécutez la commande [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Vous créez généralement votre groupe de ressources et les ressources dans une région proche de chez vous.

Une fois la commande terminée, une sortie JSON affiche les propriétés du groupe de ressources.

## <a name="create-an-azure-app-service-plan"></a>Créer un plan Azure App Service

Dans Cloud Shell, créez un plan App Service dans le groupe de ressources avec la commande [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

L’exemple suivant crée un plan App Service nommé `myAppServicePlan` dans le niveau tarifaire **Standard** (`--sku S1`) et un conteneur Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Une fois le plan App Service créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="docker-compose-configuration-options"></a>Options de configuration Docker Compose

Pour ce didacticiel, vous utilisez le fichier de composition de [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), mais vous le modifiez pour inclure Azure Database pour MySQL, le stockage persistant et Redis. Vous pouvez également utiliser une [configuration Kubernetes](#use-a-kubernetes-configuration-optional). Les fichiers de configuration se trouvent dans les [exemples Azure](https://github.com/Azure-Samples/multicontainerwordpress).

Les listes suivantes présentent des options de configuration Docker Compose prises en charge et non prises en charge dans Web App pour conteneurs :

### <a name="supported-options"></a>Options prises en charge

* command
* entrypoint
* Environnement
* image
* ports
* restart
* services
* volumes

### <a name="unsupported-options"></a>Options non prises en charge

* build (non autorisée)
* depends_on (ignorée)
* networks (ignorée)
* secrets (ignorée)

> [!NOTE]
> Les autres options qui ne sont pas explicitement appelées sont également ignorées dans la préversion publique.

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose avec conteneurs WordPress et MySQL

## <a name="create-a-docker-compose-app"></a>Création d’une application Docker Compose

Dans Cloud Shell, créez une [application web](app-service-linux-intro.md) multiconteneur dans le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). N’oubliez pas de remplacer _\<app_name>_ par un nom d’application unique.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Une fois l’application web créée, Cloud Shell affiche une sortie similaire à l’exemple suivant :

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée dans (`http://<app_name>.azurewebsites.net`). Le chargement de l’application peut prendre plusieurs minutes. Si vous recevez une erreur, attendez quelques minutes supplémentaires, puis actualisez le navigateur. Si vous rencontrez des difficultés et souhaitez résoudre les problèmes, consultez les [journaux du conteneur](#find-docker-container-logs).

![Exemple d’application à plusieurs conteneurs sur Web App pour conteneurs][1]

**Félicitations**, vous avez créé une application à plusieurs conteneurs dans Web App pour conteneurs. Ensuite, vous devez configurer votre application pour utiliser Azure Database pour MySQL. N’installez pas WordPress pour le moment.

## <a name="connect-to-production-database"></a>Connexion à la base de données de production

Il n’est pas recommandé d’utiliser des conteneurs de base de données dans un environnement de production. Les conteneurs locaux ne sont pas évolutifs. Au lieu de cela, vous allez utiliser Azure Database pour MySQL qui peut être mis à l’échelle.

### <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL

Créez un serveur Azure Database pour MySQL avec la commande [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

Dans la commande suivante, indiquez le nom unique de votre propre serveur MySQL là où se trouve l’espace réservé _&lt;mysql_server_name>_ (les caractères valides sont `a-z`, `0-9` et `-`). Ce nom fait partie du nom d’hôte du serveur MySQL (`<mysql_server_name>.database.windows.net`) et doit donc être globalement unique.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

La création du serveur peut prendre quelques minutes. Une fois le serveur MySQL créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Créez une règle de pare-feu pour votre serveur MySQL afin d’autoriser les connexions client à l’aide de la commande [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create). Lorsque les adresses IP de début et de fin sont définies sur 0.0.0.0, le pare-feu est ouvert uniquement pour les autres ressources Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Vous pouvez être encore plus restrictif dans votre règle de pare-feu en [choisissant uniquement les adresses IP sortantes que votre application utilise](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Création de la base de données WordPress

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Une fois la base de données créée, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Configuration des variables de la base de données dans WordPress

Pour connecter l’application WordPress à ce nouveau serveur MySQL, vous devez configurer quelques variables d’environnement spécifiques à WordPress, y compris le chemin d’accès de l’autorité de certification SSL défini par `MYSQL_SSL_CA`. Le [certificat racine CyberTrust Baltimore](https://www.digicert.com/digicert-root-certificates.htm) de [DigiCert](http://www.digicert.com/) est fourni dans [l’image personnalisée](https://docs.microsoft.com/en-us/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations) ci-dessous.

Pour effectuer ces modifications, utilisez la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) dans Cloud Shell. Les paramètres d’application respectent la casse et sont séparés par des espaces.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Une fois le paramètre d’application créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Utilisation d’une image personnalisée pour le protocole SSL de MySQL et d’autres configurations

Par défaut, SSL est utilisé par Azure Database pour MySQL. WordPress nécessite une configuration supplémentaire pour utiliser SSL avec MySQL. « L’image officielle » de WordPress ne fournit pas la configuration supplémentaire, mais une [image personnalisée](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/) a été préparée pour vous simplifier la tâche. En pratique, vous devez ajouter les modifications souhaitées à votre propre image.

L’image personnalisée est basée sur « l’image officielle » de [WordPress provenant du Hub Docker](https://hub.docker.com/_/wordpress/). Les modifications suivantes ont été apportées dans cette image personnalisée pour Azure Database pour MySQL :

* [Ajoute le fichier de certificat racine de CyberTrust Baltimore pour SSL à MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Utilise le paramètre d’application pour le certificat d’autorité de certification SSL de MySQL dans le fichier WordPress wp-config.php.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Ajoute une définition WordPress pour MYSQL_CLIENT_FLAGS (requise pour SSL de MySQL).](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Les modifications suivantes ont été apportées pour Redis (à utiliser dans une section ultérieure) :

* [Ajoute une extension PHP pour Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Ajoute la décompression requise pour l’extraction du fichier.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Ajoute le plug-in WordPress Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Utilise le paramètre d’application pour le nom d’hôte Redis dans le fichier WordPress wp-config.php.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Pour utiliser l’image personnalisée, vous devez mettre à jour votre fichier docker-compose-wordpress.yml. Dans Cloud Shell, tapez `nano docker-compose-wordpress.yml` pour ouvrir l’éditeur de texte nano. Remplacez `image: wordpress` par `image: microsoft/multicontainerwordpress`. Vous n’avez plus besoin du conteneur de base de données. Supprimer les sections `db`, `environment`, `depends_on` et `volumes` du fichier de configuration. Votre fichier doit ressembler au code suivant :

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Enregistrez vos modifications et quittez nano. Utilisez la commande `^O` pour enregistrer et `^X` pour quitter.

### <a name="update-app-with-new-configuration"></a>Mise à jour avec la nouvelle configuration

Dans Cloud Shell, reconfigurez votre [application web](app-service-linux-intro.md) multiconteneur avec la commande [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). N’oubliez pas de remplacer _\<app_name>_ par le nom de l’application web créée précédemment.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Une fois l’application reconfigurée, Cloud Shell affiche des informations similaires à celles de l’exemple suivant :

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée dans (`http://<app_name>.azurewebsites.net`). L’application utilise maintenant Azure Database pour MySQL.

![Exemple d’application multiconteneur sur Web App pour conteneurs][1]

## <a name="add-persistent-storage"></a>Ajout de stockage persistant

Votre conteneur à plusieurs applications s’exécute maintenant dans Web App pour conteneurs. Toutefois, si vous installez WordPress maintenant et redémarrez votre application plus tard, vous verrez que l’installation de WordPress a disparu. Cela se produit parce que votre configuration Docker Compose pointe actuellement vers un emplacement de stockage à l’intérieur de votre conteneur. Les fichiers installés dans le conteneur ne sont pas conservés après le redémarrage de l’application. Dans cette section, vous allez ajouter du stockage persistant à votre conteneur WordPress.

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Pour utiliser le stockage persistant, vous devez activer ce paramètre dans App Service. Pour faire cette modification, utilisez la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) dans Cloud Shell. Les paramètres d’application respectent la casse et sont séparés par des espaces.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Une fois le paramètre d’application créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>Modification du fichier de configuration

Dans Cloud Shell, tapez `nano docker-compose-wordpress.yml` pour ouvrir l’éditeur de texte nano.

L’option `volumes` mappe le système de fichiers sur un répertoire dans le conteneur. `${WEBAPP_STORAGE_HOME}` est une variable d’environnement d’App Service mappée sur le stockage persistant de votre application. Vous allez utiliser cette variable d’environnement dans l’option des volumes pour que les fichiers WordPress soient installés dans le stockage persistant plutôt que dans le conteneur. Apportez les modifications suivantes au fichier :

Dans la section `wordpress`, ajoutez une option `volumes` afin d’obtenir le code suivant :

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Mise à jour avec la nouvelle configuration

Dans Cloud Shell, reconfigurez votre [application web](app-service-linux-intro.md) multiconteneur avec la commande [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). N’oubliez pas de remplacer _\<app_name>_ par un nom d’application unique.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Après son exécution, la commande retourne un résultat semblable à l’exemple suivant :

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée dans (`http://<app_name>.azurewebsites.net`).

Le conteneur WordPress utilise maintenant Azure Database pour MySQL et le stockage persistant.

## <a name="add-redis-container"></a>Ajout d’un conteneur Redis

 « L’image officielle » de WordPress n’inclut pas les dépendances pour Redis. Ces dépendances et la configuration supplémentaire requise pour utiliser Redis avec WordPress ont été préparées pour vous dans cette [image personnalisée](https://github.com/Azure-Samples/multicontainerwordpress). En pratique, vous devez ajouter les modifications souhaitées à votre propre image.

L’image personnalisée est basée sur « l’image officielle » de [WordPress provenant du Hub Docker](https://hub.docker.com/_/wordpress/). Les modifications suivantes ont été apportées dans cette image personnalisée pour Redis :

* [Ajoute une extension PHP pour Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Ajoute la décompression requise pour l’extraction du fichier.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Ajoute le plug-in WordPress Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Utilise le paramètre d’application pour le nom d’hôte Redis dans le fichier WordPress wp-config.php.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Ajoutez le conteneur redis en bas du fichier de configuration afin qu’il ressemble à l’exemple suivant :

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Pour utiliser Redis, vous devez allez devoir activer ce paramètre, `WP_REDIS_HOST`, dans App Service. Pour que WordPress communique avec l’hôte Redis, ce *paramètre est obligatoire*. Pour faire cette modification, utilisez la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) dans Cloud Shell. Les paramètres d’application respectent la casse et sont séparés par des espaces.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WP_REDIS_HOST="redis"
```

Une fois le paramètre d’application créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>Mise à jour avec la nouvelle configuration

Dans Cloud Shell, reconfigurez votre [application web](app-service-linux-intro.md) multiconteneur avec la commande [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set). N’oubliez pas de remplacer _\<app_name>_ par un nom d’application unique.

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Après son exécution, la commande retourne un résultat semblable à l’exemple suivant :

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée dans (`http://<app_name>.azurewebsites.net`).

Suivez la procédure et installez WordPress.

### <a name="connect-wordpress-to-redis"></a>Connexion de WordPress à Redis

Connectez-vous à l’interface d’administration de WordPress. Dans le volet de navigation gauche, sélectionnez **Plugins** (Extensions), puis sélectionnez **Installed Plugins** (Extensions installées).

![Sélection des extensions WordPress][2]

Afficher toutes les extensions ici

Dans la page des extensions, recherchez **Redis Object Cache** et cliquez sur **Activate** (Activer).

![Activation de Redis][3]

Cliquez sur **Paramètres**.

![Cliquez sur Settings (Paramètres)][4]

Cliquez sur le bouton **Enable Object Cache** (Activer Object Cache).

![Cliquez sur le bouton Enable Object Cache (Activer Object Cache).][5]

WordPress se connecte au serveur Redis. **L’état** de la connexion apparaît sur la même page.

![WordPress se connecte au serveur Redis. **L’état** de la connexion apparaît sur la même page.][6]

**Félicitations**, vous avez connecté WordPress à Redis. L’application prête pour la production utilise maintenant **Azure Database pour MySQL, le stockage persistant et Redis**. Vous pouvez maintenant augmenter la taille des instances de votre plan App Service à plusieurs instances.

## <a name="use-a-kubernetes-configuration-optional"></a>Utilisation d’une configuration Kubernetes (facultatif)

Dans cette section, vous allez apprendre à utiliser une configuration Kubernetes pour déployer plusieurs conteneurs. Assurez-vous de suivre la procédure précédente pour créer un [groupe de ressources](#create-a-resource-group) et un [plan App Service](#create-an-azure-app-service-plan). Étant donné que cette procédure est quasiment similaire à celle de la section sur la composition, le fichier de configuration a été combiné pour vous.

### <a name="supported-kubernetes-options-for-multi-container"></a>Options Kubernetes prises en charge pour l’application à plusieurs conteneurs

* args
* command
* containers
* image
* Nom
* ports
* spec

> [!NOTE]
>Les autres options Kubernetes qui ne sont pas explicitement appelées ne sont pas prises en charge dans la préversion publique.
>

### <a name="kubernetes-configuration-file"></a>Fichier de configuration Kubernetes

Vous allez utiliser *kubernetes-wordpress.yml* pour cette partie du tutoriel. Il est montré ici à titre de référence :

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

### <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL

Créez un serveur Azure Database pour MySQL (préversion) avec la commande [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create).

Dans la commande suivante, indiquez le nom unique de votre propre serveur MySQL là où se trouve l’espace réservé _&lt;mysql_server_name>_ (les caractères valides sont `a-z`, `0-9` et `-`). Ce nom fait partie du nom d’hôte du serveur MySQL (`<mysql_server_name>.database.windows.net`) et doit donc être globalement unique.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Une fois le serveur MySQL créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Configuration d’un pare-feu de serveur

Créez une règle de pare-feu pour votre serveur MySQL afin d’autoriser les connexions client à l’aide de la commande [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create). Lorsque les adresses IP de début et de fin sont définies sur 0.0.0.0, le pare-feu est ouvert uniquement pour les autres ressources Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Vous pouvez être encore plus restrictif dans votre règle de pare-feu en [choisissant uniquement les adresses IP sortantes que votre application utilise](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Création de la base de données WordPress

Si ce n’est pas déjà fait, créez un [serveur Azure Database pour MySQL](#create-an-azure-database-for-mysql-server).

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

Une fois la base de données créée, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Configuration des variables de la base de données dans WordPress

Pour connecter l’application WordPress à ce nouveau serveur MySQL, vous devez configurer quelques variables d’environnement spécifiques à WordPress. Pour faire cette modification, utilisez la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) dans Cloud Shell. Les paramètres d’application respectent la casse et sont séparés par des espaces.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Une fois le paramètre d’application créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>Ajout de stockage persistant

Votre conteneur à plusieurs applications s’exécute maintenant dans Web App pour conteneurs. Les données seront effacées lors du redémarrage, car les fichiers n’ont pas été rendus persistants. Dans cette section, vous allez ajouter du stockage persistant à votre conteneur WordPress.

### <a name="configure-environment-variables"></a>Configuration des variables d’environnement

Pour utiliser le stockage persistant, vous devez activer ce paramètre dans App Service. Pour faire cette modification, utilisez la commande [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) dans Cloud Shell. Les paramètres d’application respectent la casse et sont séparés par des espaces.

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Une fois le paramètre d’application créé, Cloud Shell affiche des informations similaires à l’exemple suivant :

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="create-a-multi-container-app-kubernetes"></a>Création d’une application à plusieurs conteneurs (Kubernetes)

Dans Cloud Shell, créez une [application web](app-service-linux-intro.md) multiconteneur dans le groupe de ressources `myResourceGroup` et le plan App Service `myAppServicePlan` avec la commande [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). N’oubliez pas de remplacer _\<app_name>_ par un nom d’application unique.

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

Une fois l’application web créée, Cloud Shell affiche une sortie similaire à l’exemple suivant :

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
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Accéder à l’application

Accédez à l’application déployée dans (`http://<app_name>.azurewebsites.net`).

L’application exécute maintenant plusieurs conteneurs dans Web App pour conteneurs.

![Exemple d’application multiconteneur sur Web App pour conteneurs][1]

**Félicitations**, vous avez créé une application à plusieurs conteneurs dans Web App pour conteneurs.

Pour utiliser Redis, suivez la procédure dans [Connexion de WordPress à Redis](#connect-wordpress-to-redis).

## <a name="find-docker-container-logs"></a>Recherche des journaux de conteneur Docker

Si vous rencontrez des problèmes avec l’utilisation de plusieurs conteneurs, vous pouvez accéder aux journaux dans : `https://<app_name>.scm.azurewebsites.net/api/logs/docker`.

Le résultat ressemble à l’exemple suivant :

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

Vous voyez un journal pour chaque conteneur et un journal supplémentaire pour le processus parent. Copiez la valeur `href` respective dans le navigateur pour afficher le journal.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

Dans ce didacticiel, vous avez appris à :
> [!div class="checklist"]
> * Convertir une configuration Docker Compose pour travailler avec Web App pour conteneurs
> * Convertir une configuration Kubernetes pour travailler avec Web App pour conteneurs
> * Déployer une application à plusieurs conteneurs vers Azure
> * Ajouter des paramètres d’application
> * Utiliser le stockage persistant pour vos conteneurs
> * Se connecter à la base de données Azure pour MySQL
> * Résoudre les erreurs

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utiliser une image Docker personnalisée pour Web App pour conteneurs](tutorial-custom-docker-image.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
