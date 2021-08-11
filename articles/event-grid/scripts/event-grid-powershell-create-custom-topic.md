---
title: Exemple de script Azure PowerShell - Créer une rubrique personnalisée | Microsoft Docs
description: Cet article fournit un exemple de script Azure PowerShell qui montre comment créer une rubrique personnalisée Event Grid.
services: event-grid
documentationcenter: na
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2021
ms.openlocfilehash: c95f4dc48f1b6da72cad0418468ef06929ac6866
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467307"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Créer une rubrique personnalisée Event Grid avec PowerShell

Ce script crée une rubrique personnalisée Event Grid.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour créer la rubrique personnalisée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzEventGridTopic](/powershell/module/az.eventgrid/new-azeventgridtopic) | Crée une rubrique personnalisée Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/get-started-azureps).
