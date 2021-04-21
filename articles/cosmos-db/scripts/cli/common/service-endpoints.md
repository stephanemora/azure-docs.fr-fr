---
title: Créer un compte Azure Cosmos avec des points de terminaison de service de réseau virtuel
description: Créer un compte Azure Cosmos avec des points de terminaison de service de réseau virtuel
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6aa8da221818f807c29310f0b124b58ae70b853e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770721"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Créer un compte Azure Cosmos avec des points de terminaison de service de réseau virtuel avec Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.9.1 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="sample-script"></a>Exemple de script

Cet exemple crée un réseau virtuel avec un sous-réseau front-end et back-end et active des points de terminaison de service pour `Microsoft.AzureCosmosDB`. Il récupère ensuite l’ID de ressource pour ce sous-réseau, l’applique au compte Azure Cosmos et active des points de terminaison de service pour le compte.

> [!NOTE]
> Cet exemple illustre l’utilisation d’un compte d’API Core (SQL). Pour utiliser cet exemple pour d’autres API, appliquez les paramètres `enable-virtual-network` et `virtual-network-rules` du script ci-dessous au script propre à votre API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Crée un réseau virtuel Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Crée un sous-réseau pour un réseau virtuel Azure. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Retourne un sous-réseau pour un réseau virtuel Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Crée un compte Azure Cosmos DB. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface CLI Azure Cosmos DB, consultez la [documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
