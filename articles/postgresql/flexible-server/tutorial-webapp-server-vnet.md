---
title: "Tutoriel : Créer une instance d'Azure Database pour PostgreSQL - Serveur flexible et une application web Azure App Service dans le même réseau virtuel"
description: Guide de démarrage rapide expliquant comment créer une instance d'Azure Database pour PostgreSQL - Serveur flexible avec une application web dans un réseau virtuel
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ff9af90ca0b6b80ffece5ccd7d919c1d93e210c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657584"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Tutoriel : Créer une instance d'Azure Database pour PostgreSQL - Serveur flexible avec une application web App Service dans un réseau virtuel

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est disponible en préversion.

Ce tutoriel explique comment créer une application web Azure App Service avec Azure Database pour PostgreSQL - Serveur flexible (préversion) dans un [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
>[!div class="checklist"]
> * Créer un serveur flexible PostgreSQL dans un réseau virtuel
> * Créer un sous-réseau à déléguer à App Service
> * Créer une application web
> * Ajouter l'application web au réseau virtuel
> * Vous connecter à Postgres à partir de l'application web 

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Cet article nécessite que vous exécutiez localement Azure CLI version 2.0 ou ultérieure. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

Vous devrez vous connecter à votre compte à l’aide de la commande [az login](/cli/azure/authenticate-azure-cli). Notez la propriété **id** depuis la sortie de commande pour le nom d’abonnement correspondant.

```azurecli
az login
```

Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Sélectionnez l’ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account). Remplacez la propriété **ID d’abonnement** de la sortie **az login** pour votre abonnement dans l’espace réservé de l’ID d’abonnement.

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Créer un serveur flexible PostgreSQL dans un nouveau réseau virtuel

Créez un serveur flexible privé à l'intérieur d'un réseau virtuel à l'aide de la commande suivante :
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
Cette commande effectue les actions suivantes qui peuvent prendre quelques minutes :

- S'il n'existe pas encore, créez le groupe de ressources.
- Génère un nom de serveur s'il n'est pas fourni.
- Créez un réseau virtuel pour votre nouveau serveur PostgreSQL. Notez le nom du réseau virtuel et le nom du sous-réseau créés pour votre serveur car vous devez ajouter l'application web au même réseau virtuel.
- Crée un nom d'utilisateur administrateur et un mot de passe pour votre serveur, s'il n'est pas fourni.
- Crée une base de données vide appelée **postgres**

> [!NOTE]
> - Notez le mot de passe généré automatiquement s'il n'est pas fourni. Si vous oubliez le mot de passe, vous devrez le réinitialiser à l'aide de la commande ``` az postgres flexible-server update```
> - Si vous n'utilisez pas App Service Environment, vous devez activer Autoriser l'accès à partir de n'importe quelle adresse IP Azure à l'aide de cette commande. 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```

## <a name="create-subnet-for-app-service-endpoint"></a>Créer un sous-réseau pour le point de terminaison App Service
Nous avons désormais besoin d’un sous-réseau délégué au point de terminaison de l’application web App Service. Exécutez la commande suivante pour créer un sous-réseau dans le même réseau virtuel que celui sur lequel le serveur de base de données a été créé. 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
Notez le nom du réseau virtuel et le nom du sous-réseau après cette commande, car vous en aurez besoin pour ajouter une règle d’intégration au réseau virtuel pour l’application web après sa création. 

## <a name="create-a-web-app"></a>Créer une application web
Dans cette section, vous allez créer un hôte d’application dans l’application App Service, connecter cette application à la base de données Postgres, puis déployer votre code sur cet hôte. Vérifiez que vous êtes à la racine du référentiel de votre code d'application dans le terminal. Remarque : Le plan De base ne prend pas en charge l’intégration au réseau virtuel. Utilisez le niveau Standard ou Premium. 

Créez une application App Service (processus hôte) avec la commande az webapp up.

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Pour l'argument --location, utilisez le même emplacement que pour la base de données de la section précédente.
> - Remplacez <app-name> par un nom unique pour tout Azure (le point de terminaison du serveur est https://\<app-name>.azurewebsites.net). Les caractères autorisés pour <app-name> sont A-Z, 0-9, et -. Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.

Cette commande effectue les actions suivantes qui peuvent prendre quelques minutes :

- S'il n'existe pas encore, créez le groupe de ressources. (dans cette commande, vous utilisez le groupe de ressources dans lequel vous avez créé la base de données précédemment).
- Si elle n’existe pas, créez l’application App Service.
- Activez la journalisation par défaut pour l’application, si elle ne l’est pas déjà.
- Chargez le dépôt à l’aide du déploiement ZIP avec l’automatisation de la génération activée.

## <a name="add-the-web-app-to-the-virtual-network"></a>Ajouter l'application web au réseau virtuel
Utilisez la commande **az webapp vnet-integration** pour ajouter une intégration de réseau virtuel régional à une application web. Remplacez <vnet-name> et <subnet-name> par le nom du réseau virtuel et le nom du sous-réseau que le serveur flexible utilise.

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Configurer des variables d’environnement pour connecter la base de données
Le code étant maintenant déployé sur App Service, l'étape suivante consiste à connecter l'application au serveur flexible dans Azure. Le code de l’application s’attend à trouver des informations sur la base de données dans plusieurs variables d’environnement. Pour définir des variables d'environnement dans App Service, vous devez créer des « paramètres d'application » à l'aide de la commande ```az webapp config appsettings```.

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- Remplacez ```postgres-server-name```, ```username```, ```password``` par la commande de serveur flexible nouvellement créée.
- Remplacez <username> et <password> par les informations d’identification que la commande a également générées pour vous.
- Les noms du groupe de ressources et de l’application sont tirés des valeurs mises en cache dans le fichier .azure/config.
- La commande crée des paramètres nommés ```DBHOST```, ```DBNAME```, ```DBUSER``` et ```DBPASS```. Si votre code d'application utilise un autre nom pour les informations de base de données, utilisez ce nom pour les paramètres d'application, comme indiqué dans le code.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez toutes les ressources que vous avez créées dans le tutoriel à l'aide de la commande suivante. Cette commande supprime toutes les ressources de ce groupe de ressources.

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
