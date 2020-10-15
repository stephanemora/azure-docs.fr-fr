---
title: Utiliser des règles de réseau virtuel - Azure CLI - Azure Database pour PostgreSQL - Serveur unique
description: Cet article décrit comment créer et gérer des règles et points de terminaison de service de réseau virtuel pour Azure Database pour PostgreSQL à l’aide d’Azure CLI.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5048e914f07e7def81495999e5d739598a289f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710887"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Créer et gérer des points de terminaison de service de réseau virtuel pour Azure Database pour PostgreSQL – Serveur unique à l’aide d’Azure CLI
Les règles et points de terminaison de service de réseau virtuel étendent l’espace d’adressage privé d’un réseau virtuel à votre serveur Azure Database pour PostgreSQL. À l’aide de commandes d’Azure CLI pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles et points de terminaison de service de réseau virtuel pour gérer votre serveur. Pour une vue d’ensemble des points de terminaison de service de réseau virtuel Azure Database pour PostgreSQL, y compris les limitations, consultez [Use Virtual Network service endpoints and rules for Azure Database for PostgreSQL](concepts-data-access-and-security-vnet.md) (Utiliser des règles et points de terminaison de service de réseau virtuel pour Azure Database pour PostgreSQL). Les points de terminaison de service de réseau virtuel sont disponibles dans toutes les régions prises en charge pour Azure Database pour PostgreSQL.

## <a name="prerequisites"></a>Prérequis
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Installez [Azure CLI](/cli/azure/install-azure-cli) ou Azure Cloud Shell dans le navigateur.
- Un [serveur Azure Database pour PostgreSQL et une base de données](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Les points de terminaison de service de réseau virtuel sont uniquement pris en charge pour les serveurs Usage général et Mémoire optimisée.
> En cas de peering de réseau virtuel, si le trafic passe par une passerelle de réseau virtuel commune avec des points de terminaison de service et qu’il est supposé transiter par l’homologue, créez une règle ACL/VNet pour permettre aux machines virtuelles Azure du réseau virtuel de la passerelle d’accéder au serveur Azure Database pour PostgreSQL.


## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Configurer des points de terminaison de service de réseau virtuel pour Azure Database pour PostgreSQL
Les commandes [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) sont utilisées pour configurer les réseaux virtuels.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

Si vous exécutez l’interface CLI localement, vous devrez vous connecter à votre compte à l’aide de la commande [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest). Notez la propriété **id** depuis la sortie de commande pour le nom d’abonnement correspondant.
```azurecli-interactive
az login
```

Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Sélectionnez l’ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set). Remplacez la propriété **id** provenant de la sortie **az login** de votre abonnement dans l’espace réservé d’id d’abonnement.

- Le compte doit avoir les autorisations nécessaires pour créer un réseau virtuel et un point de terminaison de service.

Les points de terminaison de service peuvent être configurés indépendamment sur les réseaux virtuels par un utilisateur avec accès en écriture au réseau virtuel.

Pour sécuriser les ressources du service Azure pour un réseau virtuel, l’utilisateur doit disposer des autorisations sur « Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/ » pour les sous-réseaux à ajouter. Cette autorisation est incluse par défaut dans les rôles d’administrateur de service fédérés et peut être modifiée en créant des rôles personnalisés.

Apprenez-en davantage sur les [rôles intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) et l’affectation d’autorisations spécifiques aux [rôles personnalisés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).

Les réseaux virtuels et les ressources du service Azure peuvent être dans des abonnements identiques ou différents. Si le réseau virtuel et les ressources de service Azure se trouvent dans différents abonnements, les ressources doivent être sous le même locataire Active Directory (AD). Assurez-vous que le fournisseur de ressources **Microsoft.Sql** est inscrit pour les deux abonnements. Pour plus d’informations, reportez-vous à [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> Il est vivement recommandé de lire cet article sur les configurations de point de terminaison de service et les considérations à prendre en compte avant d’exécuter l’exemple de script ci-dessous ou de configurer les points de terminaison de service. **Point de terminaison de service de réseau virtuel :** Un [point de terminaison de service de réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md) est un sous-réseau dont les valeurs de propriétés incluent un ou plusieurs noms de type de service Azure formels. Les points de terminaison de service de réseau virtuel utilisent le nom de type de service **Microsoft.Sql**, qui fait référence au service Azure nommé SQL Database. Ce nom de service s’applique également aux services Azure SQL Database, Azure Database pour PostgreSQL et MySQL. Il est important de noter que lorsque le nom de service **Microsoft.Sql** est appliqué à un point de terminaison de service de réseau virtuel, il configure le trafic de point de terminaison de service pour l’ensemble des services Azure Database, y compris les serveurs Azure SQL Database, Azure Database pour PostgreSQL et Azure Database pour MySQL sur le sous-réseau. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exemple de script pour créer une base de données Azure Database pour PostgreSQL, un réseau virtuel, un point de terminaison de service de réseau virtuel et sécuriser le serveur au sous-réseau avec une règle de réseau virtuel
Dans cet exemple de script, modifiez les lignes en surbrillance pour personnaliser le nom d’utilisateur et le mot de passe d’administrateur. Remplacez l’ID d’abonnement utilisé dans la commande `az account set --subscription` par votre propre ID d’abonnement.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
