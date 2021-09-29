---
title: Exemple de script Azure PowerShell - Créer une rubrique personnalisée | Microsoft Docs
description: Cet article fournit un exemple de script Azure PowerShell qui montre comment créer une rubrique personnalisée Event Grid.
services: event-grid
documentationcenter: na
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2021
ms.openlocfilehash: 86ecab935209a21fbb07ae670b338f85ca455deb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680468"
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
