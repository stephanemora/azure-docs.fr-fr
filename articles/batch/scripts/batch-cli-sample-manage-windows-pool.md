---
title: Exemple de script Azure CLI - Pool Windows dans Batch
description: Ces scripts montrent certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Windows dans Azure Batch.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 773699dde9342a4b230a08471a289a56fca7e308
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768194"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Exemple CLI : Créer et gérer un pool Windows dans Azure Batch

Ces scripts montrent certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Windows dans Azure Batch. Un pool Windows peut être configuré de deux manières, soit via une configuration de services cloud, soit via une configuration de machine virtuelle. Cet exemple montre comment créer un pool Windows avec la configuration de services cloud.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ce tutoriel nécessite Azure CLI version 2.0.20 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI. |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Crée un pool de nœuds de calcul.  |
| [az batch pool set](/cli/azure/batch/pool#az_batch_pool_set) | Met à jour les propriétés d’un pool.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Active la mise à l’échelle automatique sur un pool et applique une formule.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Affiche les propriétés d’un pool.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Désactive la mise à l’échelle automatique sur un pool. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
