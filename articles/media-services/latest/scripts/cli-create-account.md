---
title: Créer un compte Azure Media Services – Azure CLI | Microsoft Docs
description: Utilisez le script Azure CLI pour créer un compte Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/25/2019
ms.author: juliako
ms.openlocfilehash: 7e668852346de1b48bd34658dea001435ba8a625
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842209"
---
# <a name="cli-example-create-an-azure-media-services-account"></a>Exemple CLI : Créer un compte Azure Media Services

Utilisez le script Azure CLI défini dans cette rubrique pour créer un compte Azure Media Services. 

Le compte Media Services et tous les comptes de stockage associés doivent être dans le même abonnement Azure. Il est recommandé d’utiliser des comptes de stockage au même emplacement que le compte Media Services.

[!INCLUDE [media-services-cli-instructions.md](../../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../../cli_scripts/media-services/media-services-create-account/Create-Account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qui lui sont associées.

```azurecli
az group delete --name amsResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crée un compte de stockage. |
| [az ams account create](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest#az-ams-account-create) | Crée un compte Media Services. |
| [az ams account sp create](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-create) | Crée un principal de service avec un mot de passe et configure son accès à un compte Azure Media Services. 
| [az group delete](/cli/azure/group#az-group-delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’exemples, consultez les [exemples Azure CLI](../cli-samples.md).
