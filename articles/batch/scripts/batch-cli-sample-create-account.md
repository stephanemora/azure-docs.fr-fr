---
title: Exemple de script Azure CLI - Créer un compte Batch - Service Batch
description: Ce script crée un compte Azure Batch dans le mode de service Batch et indique de quelle manière les différentes propriétés du compte peuvent être interrogées et mises à jour.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: labrenne
ms.openlocfilehash: 2a11e3d7b8ba2b9f4a0d0d373367575a779eebc4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017086"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>Exemple CLI : Créer un compte Batch dans le mode de service Batch

Ce script crée un compte Azure Batch dans le mode de service Batch et indique de quelle manière les différentes propriétés du compte peuvent être interrogées et mises à jour. Lorsque vous créez un compte Batch dans le mode de service Batch par défaut, ses nœuds de calcul sont attribués en interne par le service Batch. Les nœuds de calcul alloués sont soumis à un quota de processeur virtuel (principal) distinct, et le compte peut être authentifié via les informations d’identification de la clé partagée ou par un jeton Azure Active Directory.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli). 

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
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Crée le compte Batch. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crée un compte de stockage. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Met à jour les propriétés du compte Batch.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Récupère les détails relatifs au compte Batch spécifié.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Récupère les clés d’accès du compte Batch spécifié.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Effectue l’authentification par rapport au compte Batch spécifié pour renforcer les interactions avec la CLI.  |
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
