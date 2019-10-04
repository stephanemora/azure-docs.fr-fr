---
title: Exemple de script Azure PowerShell - S’abonner à une rubrique personnalisée | Microsoft Docs
description: Exemple de script Azure PowerShell - S’abonner à une rubrique personnalisée
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
ms.openlocfilehash: f16a02cd110397b1ef6bb3aa00ea12c44e4b9563
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66117133"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>S’inscrire aux événements d’une rubrique personnalisée avec PowerShell

Ce script crée un abonnement Event Grid aux événements d’une rubrique personnalisée.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

L’exemple de script en préversion requiert le module Event Grid. Pour l’installer, exécutez `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Exemple de script : stable

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Exemple de script - module en préversion

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour créer l’abonnement aux événements. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Créer un abonnement Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
