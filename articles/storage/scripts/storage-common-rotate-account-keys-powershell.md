---
title: Passer d’une clé d’accès au compte de stockage à une autre avec PowerShell
titleSuffix: Azure Storage
description: Créez un compte de stockage Azure, puis récupérez l’une de ses clés d’accès et organisez sa rotation.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: 54a7a6cb89c88726853a39ab5b2a61e1f0cde0b5
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894924"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Passer d’une clé d’accès au compte de stockage à une autre avec PowerShell

Ce script crée un compte de stockage Azure, affiche la clé d’accès primaire du nouveau compte, puis renouvelle (fait tourner) la clé.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

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
