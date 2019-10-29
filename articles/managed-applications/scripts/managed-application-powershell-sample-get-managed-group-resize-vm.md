---
title: Obtenir un groupe de ressources managé et redimensionner des machines virtuelles - Azure PowerShell
description: Fournit un exemple de script Azure PowerShell qui récupère un groupe de ressources managées pour une application managée Azure. Le script redimensionne les machines virtuelles.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f11f9b56a8a17330fac3fb50bfd5f560357463e7
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529422"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Obtenir des ressources dans un groupe de ressources managé et redimensionner des machines virtuelles avec PowerShell

Ce script récupère les ressources à partir d’un groupe de ressources managé et redimensionne les machines virtuelles dans ce groupe de ressources.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour déployer l’application managée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Répertorier les applications managées. Spécifiez un nom de groupe de ressources pour affiner les résultats. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Répertoriez les ressources. Spécifiez un groupe de ressources et un type de ressource pour affiner le résultat. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Mettre à jour la taille d’une machine virtuelle. |


## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
