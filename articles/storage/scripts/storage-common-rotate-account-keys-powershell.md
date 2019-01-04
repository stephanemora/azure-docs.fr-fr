---
title: Exemple de script Microsoft Azure PowerShell - Rotation des clés d’accès au compte de stockage | Microsoft Docs
description: Créez un compte de stockage Azure, puis récupérez l’une de ses clés d’accès et organisez sa rotation.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 026c399af70a0c97446fba28b5dd7ca1ed82b89c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635491"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Créer un compte de stockage et faire tourner ses clés d’accès

Ce script crée un compte de stockage Azure, affiche la clé d’accès primaire du nouveau compte, puis renouvelle (fait tourner) la clé.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, le compte de stockage et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer le compte de stockage, récupérer l’une de ses clés d’accès et fait tourner celle-ci. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Notes |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Obtient l’ensemble des emplacements et des fournisseurs de ressources pris en charge pour chaque emplacement. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Crée un compte de stockage. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Obtient les clés d’accès pour le compte Azure Storage. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Régénère une clé d’accès pour un compte de stockage Azure. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-powershell.md).
