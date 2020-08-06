---
title: Exemple de script Azure CLI - Exécuter une tâche Batch
description: Ce script crée une tâche Batch et ajoute une série de tâches au travail. Il explique également comment surveiller un travail et ses tâches.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8efce1bc8dc32070b770769104b7fdc38e683cd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500229"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Exemple CLI : Exécution d’un travail et de tâches avec Azure Batch

Ce script crée une tâche Batch et ajoute une série de tâches au travail. Il explique également comment surveiller un travail et ses tâches. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Crée un pool de nœuds de calcul.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Crée un travail Batch.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Ajoute une tâche au travail Batch spécifié.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Met à jour les propriétés d’un travail Batch.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Récupère les détails relatifs au travail Batch spécifié.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Récupère les détails relatifs à une tâche du travail Batch spécifié.  |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
