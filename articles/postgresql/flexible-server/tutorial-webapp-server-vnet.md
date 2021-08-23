---
title: "Tutoriel : Créer une instance d'Azure Database pour PostgreSQL - Serveur flexible et une application web Azure App Service dans le même réseau virtuel"
description: Guide de démarrage rapide expliquant comment créer une instance d'Azure Database pour PostgreSQL - Serveur flexible avec une application web dans un réseau virtuel
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/30/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 58e7357552be9c209dec81cc9ace23fcfd82ea2d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467131"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>Tutoriel : Créer une instance d'Azure Database pour PostgreSQL - Serveur flexible avec une application web App Service dans un réseau virtuel

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est disponible en préversion.

Ce tutoriel explique comment créer une application web Azure App Service avec Azure Database pour PostgreSQL - Serveur flexible (préversion) dans un [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
>[!div class="checklist"]
> * Créer un serveur flexible PostgreSQL dans un réseau virtuel
> * Créer une application web
> * Ajouter l'application web au réseau virtuel
> * Vous connecter à Postgres à partir de l'application web 

## <a name="prerequisites"></a>Prérequis

- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- [Installez Azure CLI](/cli/azure/install-azure-cli). version 2.0 ou ultérieure localement. Pour afficher la version installée, exécutez la commande `az --version`. 
- Connectez-vous à votre compte à l’aide de la commande [az login](/cli/azure/authenticate-azure-cli). Notez la propriété **id** depuis la sortie de commande pour le nom d’abonnement correspondant.

  ```azurecli
  az login
  ```
- Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Sélectionnez l’ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account).

  ```azurecli
  az account set --subscription <subscription ID>
  ```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>Créer un serveur flexible PostgreSQL dans un nouveau réseau virtuel

Créez un serveur flexible privé à l'intérieur d'un réseau virtuel à l'aide de la commande suivante :

```azurecli
az postgres flexible-server create --resource-group demoresourcegroup --name demoserverpostgres --vnet demoappvnet --location westus2
```
Cette commande effectue les actions suivantes qui peuvent prendre quelques minutes :

- S'il n'existe pas encore, créez le groupe de ressources.
- Génère un nom de serveur s'il n'est pas fourni.
- Créez un réseau virtuel pour votre nouveau serveur et sous-réseau postgreSQL au sein de ce réseau virtuel pour le serveur de base de données.
- Crée un nom d'utilisateur administrateur et un mot de passe pour votre serveur, s'il n'est pas fourni.
- Crée une base de données vide appelée **postgres**

Voici l’exemple de sortie.

```json
Local context is turned on. Its information is saved in working directory /home/jane. You can run `az local-context off` to turn it off.
Command argument values from local context: --resource-group demoresourcegroup, --location: eastus
Command group 'postgres flexible-server' is in preview. It may be changed/removed in a future release.
Checking the existence of the resource group ''...
Creating Resource group 'demoresourcegroup ' ...
Creating new vnet "demoappvnet" in resource group "demoresourcegroup" ...
Creating new subnet "Subnet095447391" in resource group "demoresourcegroup " and delegating it to "Microsoft.DBforPostgreSQL/flexibleServers"...
Creating PostgreSQL Server 'demoserverpostgres' in group 'demoresourcegroup'...
Your server 'demoserverpostgres' is using sku 'Standard_D2s_v3' (Paid Tier). Please refer to https://aka.ms/postgres-pricing for pricing details
Make a note of your password. If you forget, you would have to resetyour password with 'az postgres flexible-server update -n demoserverpostgres --resource-group demoresourcegroup -p <new-password>'.
{
  "connectionString": "postgresql://generated-username:generated-password@demoserverpostgres.postgres.database.azure.com/postgres?sslmode=require",
  "host": "demoserverpostgres.postgres.database.azure.com",
  "id": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/demoserverpostgres",
  "location": "East US",
  "password": "generated-password",
  "resourceGroup": "demoresourcegroup",
  "skuname": "Standard_D2s_v3",
  "subnetId": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.Network/virtualNetworks/VNET095447391/subnets/Subnet095447391",
  "username": "generated-username",
  "version": "12"
}
```

## <a name="create-a-web-app"></a>Créer une application web
Dans cette section, vous allez créer un hôte d’application dans l’application App Service, connecter cette application à la base de données Postgres, puis déployer votre code sur cet hôte. Vérifiez que vous êtes à la racine du référentiel de votre code d'application dans le terminal. Remarque : Le plan De base ne prend pas en charge l’intégration au réseau virtuel. Utilisez le niveau Standard ou Premium. 

Créez une application App Service (processus hôte) avec la commande az webapp up.

```azurecli
az webapp up --resource-group demoresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - Pour l'argument --location, utilisez le même emplacement que pour la base de données de la section précédente.
> - Remplacez <app-name> par un nom unique dans l’ensemble d’Azure. Les caractères autorisés pour <app-name> sont A-Z, 0-9, et -. Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.

Cette commande effectue les actions suivantes qui peuvent prendre quelques minutes :

- S'il n'existe pas encore, créez le groupe de ressources. (dans cette commande, vous utilisez le groupe de ressources dans lequel vous avez créé la base de données précédemment).
- Si elle n’existe pas, créez l’application App Service.
- Activez la journalisation par défaut pour l’application, si elle ne l’est pas déjà.
- Chargez le dépôt à l’aide du déploiement ZIP avec l’automatisation de la génération activée.

### <a name="create-subnet-for-web-app"></a>Créer un sous-réseau pour une application web
Avant d’activer l’intégration au réseau virtuel, vous devez disposer d’un sous-réseau délégué à l’application Web App Service. Avant de créer le sous-réseau, affichez l’adresse du sous-réseau de base de données pour éviter d’utiliser le même préfixe d’adresse pour le sous-réseau d’application Web. 

```azurecli
az network vnet show --resource-group demoresourcegroup -n demoappvnet
```

Exécutez la commande suivante pour créer un sous-réseau dans le même réseau virtuel que celui sur lequel le serveur de base de données a été créé. **Mettez à jour le préfixe d’adresse pour éviter les conflits avec le sous-réseau de base de données.**

```azurecli
az network vnet subnet create --resource-group demoresourcegroup --vnet-name demoappvnet --name webappsubnet  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms
```

## <a name="add-the-web-app-to-the-virtual-network"></a>Ajouter l'application web au réseau virtuel
Utilisez la commande [az webapp vnet-integration](/cli/azure/webapp/vnet-integration) pour ajouter une intégration de réseau virtuel régional à une application web. 

```azurecli
az webapp vnet-integration add --resource-group demoresourcegroup -n  mywebapp --vnet demoappvnet --subnet webappsubnet
```

## <a name="configure-environment-variables-to-connect-the-database"></a>Configurer des variables d’environnement pour connecter la base de données
Le code étant maintenant déployé sur App Service, l'étape suivante consiste à connecter l'application au serveur flexible dans Azure. Le code de l’application s’attend à trouver des informations sur la base de données dans plusieurs variables d’environnement. Pour définir des variables d’environnement dans App Service, utilisez [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set).

  
```azurecli
  
az webapp config appsettings set  --name mywebapp --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>" 
```
- Remplacez **postgres-server-name**,**username**,**password** pour la commande de serveur flexible nouvellement créée.
- Remplacez **<username>** et **<password>** par les informations d’identification que la commande a également générées pour vous.
- Les noms du groupe de ressources et de l’application sont tirés des valeurs mises en cache dans le fichier .azure/config.
- La commande crée les paramètres **DBHOST**, **DBNAME**, **DBUSER** _ et _*DBPASS**. Si votre code d'application utilise un autre nom pour les informations de base de données, utilisez ce nom pour les paramètres d'application, comme indiqué dans le code.

Configurez l’application Web pour autoriser toutes les connexions sortantes à partir du réseau virtuel.
```azurecli
az webapp config set --name mywebapp --resource-group demoresourcegroup --generic-configurations '{"vnetRouteAllEnabled": true}'
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez toutes les ressources que vous avez créées dans le tutoriel à l'aide de la commande suivante. Cette commande supprime toutes les ressources de ce groupe de ressources.

```azurecli
az group delete -n demoresourcegroup
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Mapper un nom DNS personnalisé existant à Azure App Service](../../app-service/app-service-web-tutorial-custom-domain.md)
