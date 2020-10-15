---
title: Exemple de script Azure CLI - Pool Windows dans Batch
description: Ces scripts montrent certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Windows dans Azure Batch.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8863831d236581f07df85acda26268659e3a7d8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500263"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Exemple CLI : Créer et gérer un pool Windows dans Azure Batch

Ces scripts montrent certaines des commandes disponibles dans l’interface Azure CLI pour créer et gérer un pool de nœuds de calcul Windows dans Azure Batch. Un pool Windows peut être configuré de deux manières, soit via une configuration de services cloud, soit via une configuration de machine virtuelle. Cet exemple montre comment créer un pool Windows avec la configuration de services cloud.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

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
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Crée le compte Batch. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI. |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Crée un pool de nœuds de calcul.  |
| [az batch pool set](/cli/azure/batch/pool#az-batch-pool-set) | Met à jour les propriétés d’un pool.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | Active la mise à l’échelle automatique sur un pool et applique une formule.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Affiche les propriétés d’un pool.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | Désactive la mise à l’échelle automatique sur un pool. |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
