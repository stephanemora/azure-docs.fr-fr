---
title: Gérer les points de terminaison de réseau virtuel - Azure CLI - Azure Database for MariaDB
description: Cet article décrit comment créer et gérer des règles et points de terminaison de service de réseau virtuel Azure Database for MariaDB à l’aide d’Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: eea6f6751f7e7f6ded1b1c7004115d101f1dcc94
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764335"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Créer et gérer des règles et points de terminaison de service de réseau virtuel Azure Database for MariaDB à l’aide d’Azure CLI

Les règles et points de terminaison de service de réseau virtuel étendent l’espace d’adressage privé d’un réseau virtuel à votre serveur Azure Database for MariaDB. À l’aide de commandes d’Azure CLI pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles et points de terminaison de service de réseau virtuel pour gérer votre serveur. Pour avoir une vue d’ensemble des points de terminaison de service de réseau virtuel Azure Database for MariaDB, y compris les limitations, consultez [Utiliser des règles et points de terminaison de service de réseau virtuel pour Azure Database for MariaDB](concepts-data-access-security-vnet.md). Les points de terminaison de service de réseau virtuel sont disponibles dans toutes les régions prenant en charge Azure Database for MariaDB.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Installez [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur.
- Un [serveur Azure Database for MariaDB et une base de données](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.

## <a name="configure-vnet-service-endpoints"></a>Configurer des points de terminaison de service de réseau virtuel
Les commandes [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) sont utilisées pour configurer les réseaux virtuels.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli). 

Si vous exécutez l’interface CLI localement, vous devrez vous connecter à votre compte à l’aide de la commande [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Notez la propriété **id** depuis la sortie de commande pour le nom d’abonnement correspondant.
```azurecli-interactive
az login
```

Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Sélectionnez l’ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Remplacez la propriété **id** provenant de la sortie **az login** de votre abonnement dans l’espace réservé d’id d’abonnement.

- Le compte doit avoir les autorisations nécessaires pour créer un réseau virtuel et un point de terminaison de service.

Les points de terminaison de service peuvent être configurés indépendamment sur les réseaux virtuels par un utilisateur avec accès en écriture au réseau virtuel.

Pour sécuriser les ressources du service Azure pour un réseau virtuel, l’utilisateur doit disposer des autorisations sur « Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/ » pour les sous-réseaux à ajouter. Cette autorisation est incluse par défaut dans les rôles d’administrateur de service fédérés et peut être modifiée en créant des rôles personnalisés.

Apprenez-en davantage sur les [rôles intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Les réseaux virtuels et les ressources du service Azure peuvent être dans des abonnements identiques ou différents. Si le réseau virtuel et les ressources de service Azure se trouvent dans différents abonnements, les ressources doivent être sous le même locataire Active Directory (AD). Assurez-vous que le fournisseur de ressources **Microsoft.Sql** est inscrit pour les deux abonnements. Pour plus d’informations, reportez-vous à [, resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> Il est vivement recommandé de lire cet article sur les configurations de point de terminaison de service et les considérations à prendre en compte avant de configurer les points de terminaison de service. **Point de terminaison de service de réseau virtuel :** Un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Les points de terminaison de service de réseau virtuel utilisent le nom de type de service **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database. Ce nom de service s’applique également aux services Azure SQL Database, Azure Database for MariaDB et MySQL. Il est important de noter que lorsque le nom de service **Microsoft.Sql** est appliqué à un point de terminaison de service de réseau virtuel, il configure le trafic de point de terminaison de service pour l’ensemble des services Azure Database, y compris les serveurs Azure SQL Database, Azure Database pour PostgreSQL, Azure Database for MariaDB et Azure Database pour MySQL dans le sous-réseau.

### <a name="sample-script"></a>Exemple de script

Cet exemple de script permet de créer un serveur Azure Database for MariaDB, un réseau virtuel ainsi qu’un point de terminaison de service de réseau virtuel, et de sécuriser le serveur au niveau du sous-réseau avec une règle de réseau virtuel. Dans cet exemple de script, changez le nom d’utilisateur et le mot de passe de l’administrateur. Remplacez l’ID d’abonnement utilisé dans la commande `az account set --subscription` par votre propre ID d’abonnement.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/resource-manager-supported-services.md