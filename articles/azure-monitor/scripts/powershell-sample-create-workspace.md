---
title: Créer un espace de travail Log Analytics - Azure PowerShell
description: Exemple de script Azure PowerShell - Créer un espace de travail Log Analytics pour
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80054631"
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

