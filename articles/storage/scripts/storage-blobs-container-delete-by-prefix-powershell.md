---
title: Exemple de script Azure PowerShell - Supprimer des conteneurs par préfixe | Microsoft Docs
description: Supprimez des conteneurs d’objets blob Stockage Azure selon un préfixe de nom de conteneur.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 73c2f221d2ac0b3ceb76e3db0fdc6379a6dd330a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060879"
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

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-powershell.md).
