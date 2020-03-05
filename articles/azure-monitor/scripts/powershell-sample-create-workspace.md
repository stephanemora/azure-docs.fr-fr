---
title: Exemple de script Azure PowerShell - Créer un espace de travail Log Analytics | Microsoft Docs
description: Exemple de script Azure PowerShell - Créer un espace de travail Log Analytics pour
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: fc60be2364e80c288300d78cc5dd23eed4ea9e62
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658147"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Créer un espace de travail Log Analytics avec PowerShell

Ce script vous permet de démarrer rapidement avec un espace de travail Azure Log Analytics, qui est nécessaire si vous souhaitez collecter, analyser et prendre des mesures sur les données.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un nouvel espace de travail Log Analytics dans votre abonnement. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Obtient des informations sur un espace de travail existant. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Crée un espace de travail dans le groupe de ressources et l’emplacement spécifiés. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

