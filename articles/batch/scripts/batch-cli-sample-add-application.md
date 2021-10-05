---
title: Exemple de script Azure CLI - Ajout d’une application dans Batch | Microsoft Docs
description: Découvrez comment ajouter une application pour l’utiliser avec un pool Azure Batch ou une tâche à l’aide de l’interface Azure CLI.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: traitement, lot, exemples azure cli, exemples de code azure cli, exemples de scripts azure cli
ms.openlocfilehash: 1c20adef86e30df79a5de2d6229ca099875c98f0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595196"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>Exemple CLI : Ajout d’une application à un compte Azure Batch

Ce script explique comment ajouter une application à utiliser avec une tâche ou un pool Azure Batch. Pour configurer une application à ajouter à votre compte Batch, placez votre fichier exécutable et toutes ses éventuelles dépendances dans un fichier .zip. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ce tutoriel nécessite Azure CLI version 2.0.20 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes.
Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crée le compte Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | Crée une application.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | Ajoute un package d’application à l’application spécifiée.  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | Met à jour les propriétés d’une application.  |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
