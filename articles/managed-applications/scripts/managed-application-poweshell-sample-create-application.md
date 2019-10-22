---
title: Exemple de script Azure PowerShell - Déployer une application managée | Microsoft Docs
description: Fournit un exemple de script Azure PowerShell qui déploie une définition d’application managée dans l’abonnement.
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
ms.openlocfilehash: d450c2292f5d2982b7c8b266cc6780a72d426321
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332572"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Déployer une application managée pour un catalogue de services avec PowerShell

Ce script déploie une définition d’application managée à partir du catalogue de services.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour déployer l’application managée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplication) | Créer une application managée. Fournir les paramètres et l’ID de définition pour le modèle. |


## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
