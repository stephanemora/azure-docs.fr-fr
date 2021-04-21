---
title: Connecter un compte Azure Cosmos existant avec des points de terminaison de service de réseau virtuel
description: Connecter un compte Azure Cosmos existant avec des points de terminaison de service de réseau virtuel
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: eae343b0ac85f3fdf6d0f6d52c7afbb91f401df4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770738"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Connecter un compte Azure Cosmos existant à des points de terminaison de service de réseau virtuel avec Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Cet article nécessite la version 2.9.1 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="sample-script"></a>Exemple de script

Cet exemple vise à montrer comment connecter un compte Azure Cosmos existant à un nouveau réseau virtuel dont le sous-réseau n’est pas encore configuré pour les points de terminaison de service quand le paramètre `ignore-missing-vnet-service-endpoint` est utilisé. Cela permet de configurer le compte Cosmos sans erreur avant de configurer le sous-réseau du réseau virtuel. Une fois le sous-réseau configuré, le compte Cosmos est alors accessible via le sous-réseau configuré.

> [!NOTE]
> Cet exemple illustre l’utilisation d’un compte d’API SQL (Core). Pour utiliser cet exemple pour d’autres API, appliquez les paramètres `enable-virtual-network` et `virtual-network-rules` du script ci-dessous au script propre à votre API.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) | Met à jour un sous-réseau pour un réseau virtuel Azure. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface CLI Azure Cosmos DB, consultez la [documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
