---
title: 'Exemple de script Azure PowerShell : obtenir un groupe de ressources managé et redimensionner des machines virtuelles | Microsoft Docs'
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
ms.openlocfilehash: f36ed42e0b7f8e4d1e5b092d2a14fd0cd8178ee0
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330121"
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
