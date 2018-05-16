---
title: Exemple de script Azure CLI - Créer un compte Azure Media Services | Microsoft Docs
description: Utilisez le script Azure CLI pour créer un compte Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: def590fa8253d81f3477c3953db684c160e25cd9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Exemple CLI : Créer un compte Azure Media Services

Ce script crée un compte Azure Media Services.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.20 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/media-services-create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Crée un compte de stockage. |
| **az ams account create** | Crée un compte Media Services. |
| **az ams account sp create** | Crée un principal de service avec un mot de passe et configure son accès à un compte Azure Media Services. |
| [az group delete](/cli/azure/group#az_group_delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples, consultez les [exemples Azure CLI](../cli-samples.md).
