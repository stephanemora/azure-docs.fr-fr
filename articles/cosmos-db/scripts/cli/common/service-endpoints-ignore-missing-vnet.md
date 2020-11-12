---
title: Connecter un compte Azure Cosmos existant avec des points de terminaison de service de réseau virtuel
description: Connecter un compte Azure Cosmos existant avec des points de terminaison de service de réseau virtuel
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 8e543e21835b14cc45bcf6f0cac1c8d2b821cc0e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310905"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Connecter un compte Azure Cosmos existant à des points de terminaison de service de réseau virtuel avec Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cette rubrique vous impose d’exécuter Azure CLI version 2.9.1 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

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
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Crée un réseau virtuel Azure. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Crée un sous-réseau pour un réseau virtuel Azure. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Retourne un sous-réseau pour un réseau virtuel Azure. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Crée un compte Azure Cosmos DB. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Met à jour un sous-réseau pour un réseau virtuel Azure. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface CLI Azure Cosmos DB, consultez la [documentation sur l’interface CLI Azure Cosmos DB](/cli/azure/cosmosdb).

Vous trouverez tous les exemples de scripts CLI Azure Cosmos DB dans le [dépôt GitHub CLI Azure Cosmos DB](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
