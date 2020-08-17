---
title: Exemple de script Azure PowerShell - Supprimer des conteneurs par préfixe | Microsoft Docs
description: Lisez un exemple montrant comment supprimer un Stockage Blob Azure en fonction d’un préfixe dans le nom du conteneur, à l’aide d’Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 18827beeb606694e2c9089f27570216d413aabd9
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033544"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Supprimer des conteneurs selon un préfixe de nom de conteneur

Ce script supprime des conteneurs dans le stockage d’objets blob Azure selon un préfixe contenu dans le nom du conteneur.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Exécutez la commande suivante pour supprimer le groupe de ressources, les conteneurs restants, ainsi que toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour supprimer des conteneurs selon un préfixe de nom de conteneur. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Notes |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtient un compte de stockage spécifié ou tous les comptes de stockage dans un groupe de ressources ou l’abonnement. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Répertorie les conteneurs de stockage associés à un compte de stockage. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Supprime le conteneur de stockage spécifié. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-powershell.md).
