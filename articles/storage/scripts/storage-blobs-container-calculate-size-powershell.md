---
title: Exemple de script Azure PowerShell - Calculer la taille d’un conteneur d’objets blob | Microsoft Docs
description: Calculez la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles de chacun de ses objets blob.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: ec8254478786825d0333e8be24b0dfbea8c18e60
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245148"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calculer la taille totale d’un conteneur de stockage d’objets blob

Ce script calcule la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles des objets blob inclus dans le conteneur.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Ce script PowerShell fournit une estimation de taille pour le conteneur et ne doit pas être utilisé pour les calculs de facturation. Pour un script qui calcule la taille du conteneur pour la facturation, consultez [Calculer la taille totale d’un conteneur de stockage d’objets blob](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, le conteneur et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour calculer la taille du conteneur de stockage d’objets blob. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Notes |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Obtient un compte de stockage spécifié ou tous les comptes de stockage dans un groupe de ressources ou l’abonnement. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Répertorie les objets blob inclus dans un conteneur. ||

## <a name="next-steps"></a>Étapes suivantes

Pour un script qui calcule la taille du conteneur pour la facturation, consultez [Calculer la taille totale d’un conteneur de stockage d’objets blob](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour Stockage Azure](../blobs/storage-samples-blobs-powershell.md).
