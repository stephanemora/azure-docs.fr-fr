---
title: Créer et gérer des règles et points de terminaison de service de réseau virtuel Azure Database pour MySQL à l’aide d’Azure CLI | Microsoft Docs
description: Cet article décrit comment créer et gérer des règles et points de terminaison de service de réseau virtuel Azure Database pour MySQL à l’aide d’Azure CLI.
services: mysql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: fd8b21d1273b1bd02b0a949894be53cdc4a5c3c0
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736590"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Créer et gérer des règles et points de terminaison de service de réseau virtuel Azure Database pour MySQL à l’aide d’Azure CLI
Les règles et points de terminaison de service de réseau virtuel étendent l’espace d’adressage privé d’un réseau virtuel à votre serveur Azure Database pour MySQL. À l’aide de commandes d’Azure CLI pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles et points de terminaison de service de réseau virtuel pour gérer votre serveur. Pour une vue d’ensemble des points de terminaison de service de réseau virtuel Azure Database pour MySQL, y compris les limitations, consultez [Use Virtual Network service endpoints and rules for Azure Database for PostgreSQL](concepts-data-access-and-security-vnet.md) (Utiliser des règles et points de terminaison de service de réseau virtuel pour Azure Database pour PostgreSQL). Les points de terminaison de service de réseau virtuel sont disponibles en préversion publique dans toutes les régions prises en charge pour Azure Database pour MySQL.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Installer l’utilitaire de ligne de commande [Azure CLI 2.0](/cli/azure/install-azure-cli) ou utiliser Azure Cloud Shell dans le navigateur.
- Un [serveur et une base de données Azure Database pour MySQL](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Mémoire optimisée et Usage général.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Configurer des points de terminaison de service de réseau virtuel pour Azure Database pour MySQL
Les commandes [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) sont utilisées pour configurer les réseaux virtuels.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Si vous exécutez l’interface CLI localement, vous devrez vous connecter à votre compte à l’aide de la commande [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in). Notez la propriété **id** depuis la sortie de commande pour le nom d’abonnement correspondant.
```azurecli-interactive
az login
```

Installez l’extension CLI pour les points de terminaison de service de réseau virtuel Azure Database pour MySQL à l’aide de la commande `az extension add --name rdbms-vnet`. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Exécutez la commande `az extension list` pour vérifier l’installation de l’extension CLI.
```azurecli-interactive
az extension list
```
La sortie de la commande répertorie toutes les extensions installées. L’extension CLI pour Azure Database pour MySQL est :

 { "extensionType": "whl", "name": "rdbms-vnet", "version": "10.0.0" }

> [!NOTE]
> Pour désinstaller l’extension CLI, exécutez la commande `az extension remove -n rdbms-vnet`. 

Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Sélectionnez l’ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account#az_account_set). Remplacez la propriété **id** provenant de la sortie **az login** de votre abonnement dans l’espace réservé d’id d’abonnement.

- Le compte doit avoir les autorisations nécessaires pour créer un réseau virtuel et un point de terminaison de service.

Les points de terminaison de service peuvent être configurés indépendamment sur les réseaux virtuels par un utilisateur avec accès en écriture au réseau virtuel.

Afin de sécuriser les ressources du service Azure pour un réseau virtuel, l’utilisateur doit disposer des autorisations pour « Microsoft.Network/JoinServicetoaSubnet » pour les sous-réseaux à ajouter. Cette autorisation est incluse par défaut dans les rôles d’administrateur de service fédérés et peut être modifiée en créant des rôles personnalisés.

Apprenez-en davantage sur les [rôles intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Les réseaux virtuels et les ressources du service Azure peuvent être dans des abonnements identiques ou différents. Si le réseau virtuel et les ressources de service Azure se trouvent dans différents abonnements, les ressources doivent être dans le même locataire Active Directory (AD) au moment de cette préversion.

> [!IMPORTANT]
> Il est vivement recommandé de lire cet article sur les configurations de point de terminaison de service et les considérations à prendre en compte avant d’exécuter l’exemple de script ci-dessous ou de configurer les points de terminaison de service. **Point de terminaison de service de réseau virtuel :** un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Les points de terminaison de service de réseau virtuel utilisent le nom de type de service **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database. Ce nom de service s’applique également aux services Azure SQL Database, Azure Database pour PostgreSQL et MySQL. Il est important de noter que lorsque le nom de service **Microsoft.Sql** est appliqué à un point de terminaison de service de réseau virtuel, il configure le trafic de point de terminaison de service pour l’ensemble des services Azure Database, y compris les serveurs Azure SQL Database, Azure Database pour PostgreSQL et Azure Database pour MySQL sur le sous-réseau. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exemple de script pour créer une base de données Azure Database pour MySQL, un réseau virtuel, ainsi qu’un point de terminaison de service de réseau virtuel, et pour sécuriser le serveur au niveau du sous-réseau avec une règle de réseau virtuel
Dans cet exemple de script, modifiez les lignes en surbrillance pour personnaliser le nom d’utilisateur et le mot de passe d’administrateur. Remplacez l’ID d’abonnement utilisé dans la commande `az account set --subscription` par votre propre ID d’abonnement.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
