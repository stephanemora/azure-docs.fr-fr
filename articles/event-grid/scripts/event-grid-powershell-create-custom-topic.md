---
title: Exemple de script Azure PowerShell - Créer une rubrique personnalisée | Microsoft Docs
description: Exemple de script Azure PowerShell - Créer une rubrique personnalisée
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: 20ad58c2e7209f8bed8014d5ce1dff094907590c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117152"
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
| [New-AzEventGridTopic](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridtopic) | Crée une rubrique personnalisée Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
