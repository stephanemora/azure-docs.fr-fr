---
title: Exemple de script Azure CLI - Pool Linux dans Batch
description: Ce script montre certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Linux dans Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.openlocfilehash: e39deaf2cb897e01ac69b26a7855d7227904539a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681869"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Exemple d’interface CLI : créer et gérer un pool de Linux dans Azure Batch

Ce script montre certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Linux dans Azure Batch.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande en local, ce démarrage rapide nécessite que vous exécutiez la version 2.0.20 minimum d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

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
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Crée le compte Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Répertorie les SKU de l’agent de nœud et les informations de l’image.  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | Crée un pool de nœuds de calcul.  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-resize) | Redimensionne le nombre de machines virtuelles en cours d’exécution dans le pool spécifié.  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | Affiche les propriétés d’un pool.  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-list) | Répertorie tous les nœuds de calcul dans le pool spécifié.  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-reboot) | Redémarre le nœud de calcul spécifié.  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-delete) | Supprime les nœuds répertoriés dans le pool spécifié.  |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).
