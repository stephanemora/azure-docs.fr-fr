---
title: Exemple de script Azure CLI - Créer un compte Batch - Service Batch | Microsoft Docs
description: Découvrez comment créer un compte Batch en mode de service Batch avec cet exemple de script Azure CLI. Ce script montre également comment interroger ou mettre à jour différentes propriétés du compte.
ms.topic: sample
ms.date: 09/17/2021
ms.custom: devx-track-azurecli, seo-azure-cli
keywords: traitement, lot, exemples azure cli, exemples de code azure cli, exemples de scripts azure cli
ms.openlocfilehash: 0e909f644fddae91a664b11abdbba0ae8c71b2ab
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548386"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Exemple CLI : Créer un compte Batch dans le mode de service Batch

Ce script crée un compte Azure Batch dans le mode de service Batch et indique de quelle manière les différentes propriétés du compte peuvent être interrogées et mises à jour. Lorsque vous créez un compte Batch dans le mode de service Batch par défaut, ses nœuds de calcul sont attribués en interne par le service Batch. Les nœuds de calcul alloués sont soumis à un quota de processeur virtuel (principal) distinct, et le compte peut être authentifié via les informations d’identification de la clé partagée ou par un jeton Azure Active Directory.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ce tutoriel nécessite Azure CLI version 2.0.20 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée. 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Met à jour les propriétés du compte Batch.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Récupère les détails relatifs au compte Batch spécifié.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Récupère les clés d’accès du compte Batch spécifié.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
