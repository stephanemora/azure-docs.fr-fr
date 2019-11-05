---
title: Exemple de script Azure CLI - S’abonner à un abonnement Azure | Microsoft Docs
description: Exemple de script Azure CLI - S’abonner à un abonnement Azure
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: 9d1aa53ede323c2bb536c74eeaaba9fd28b01712
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549302"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-azure-cli"></a>S’abonner aux événements d’un abonnement Azure avec Azure CLI

Ce script crée un abonnement Event Grid aux événements d’un abonnement Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

L’exemple de script en préversion requiert l’extension Event Grid. Pour l’installer, exécutez `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exemple de script : stable

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.sh "Subscribe to Azure subscription")]

## <a name="sample-script---preview-extension"></a>Exemple de script : extension en préversion

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.sh "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour créer l’abonnement aux événements. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Créer un abonnement Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) : version d’extension | Créer un abonnement Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’interrogation d’abonnements, consultez [Interroger les abonnements Event Grid](../query-event-subscriptions.md).
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).
