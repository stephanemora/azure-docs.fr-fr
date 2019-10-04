---
title: Exemple de script Azure PowerShell - Déployer une application managée | Microsoft Docs
description: Exemple de script Azure PowerShell - Déployer une définition d’application managée
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
ms.openlocfilehash: 03b453e441b92d21f29e7e1f401bebae0b8589da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171452"
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
