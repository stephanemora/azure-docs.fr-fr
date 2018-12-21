---
title: Exemple de script Azure CLI – Créer un cluster Batch AI dédié | Microsoft Docs
description: Exemple de script Azure CLI – Créer et gérer un cluster Batch AI avec des nœuds dédiés (machines virtuelles)
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 643d2a5a600d018912f09d01f2e018100767f147
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410123"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-dedicated-nodes"></a>Exemple CLI : Créer et gérer un cluster Batch AI de nœuds dédiés

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Ce script présente certaines des commandes disponibles dans Azure CLI pour créer et gérer un cluster Batch AI composé de nœuds dédiés (machines virtuelles).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, ce démarrage rapide exige au minimum la version 2.0.38 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-dedicated.sh "Create Batch AI cluster - dedicated nodes")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez les commandes suivantes pour supprimer les groupes de ressources et toutes les ressources associées.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Crée un espace de travail Batch AI. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Crée un cluster Batch AI. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Affiche des informations sur un cluster Batch AI. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node#az-batchai-cluster-show) | Liste les nœuds dans un cluster Batch AI. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Redimensionne le cluster Batch AI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).
