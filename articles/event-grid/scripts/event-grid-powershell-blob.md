---
title: Azure PowerShell - S’abonner à un compte de stockage blob
description: Cet article fournit un exemple de script Azure PowerShell qui montre comment s’abonner à des événements Event Grid pour un compte Stockage Blob.
ms.devlang: powershell
ms.topic: sample
ms.date: 09/15/2021
ms.openlocfilehash: 72899bf83d7610576d36a1670cd589d68291baf3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602875"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-powershell"></a>S’abonner aux événements d’un compte de stockage blob avec PowerShell

Ce script crée un abonnement Event Grid aux événements d’un compte de stockage blob.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Exemple de script : stable

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.ps1 "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour créer l’abonnement aux événements. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Créer un abonnement Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](../overview.md).
* Pour plus d’informations sur PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/get-started-azureps).
