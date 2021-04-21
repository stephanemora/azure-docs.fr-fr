---
title: Exemple de script Azure CLI - Pool Linux dans Batch
description: Ce script montre certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Linux dans Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b5e1bdccefffa7803fbe744e27c1b36ca719560d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768284"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Exemple d’interface CLI : créer et gérer un pool de Linux dans Azure Batch

Ce script montre certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Linux dans Azure Batch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ce tutoriel nécessite Azure CLI version 2.0.20 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crée le compte Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Répertorie les SKU de l’agent de nœud et les informations de l’image.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Crée un pool de nœuds de calcul.  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | Redimensionne le nombre de machines virtuelles en cours d’exécution dans le pool spécifié.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Affiche les propriétés d’un pool.  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | Répertorie tous les nœuds de calcul dans le pool spécifié.  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | Redémarre le nœud de calcul spécifié.  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | Supprime les nœuds répertoriés dans le pool spécifié.  |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
