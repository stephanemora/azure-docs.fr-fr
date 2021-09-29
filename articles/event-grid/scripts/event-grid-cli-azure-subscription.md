---
title: Exemple de script Azure CLI - S’abonner à un abonnement Azure | Microsoft Docs
description: Cet article fournit un exemple de script Azure CLI qui montre comment s’abonner à des événements Azure Event Grid avec Azure CLI.
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b12e7e530f83d599037713b2f372a49e9a4b927
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649432"
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
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Créer un abonnement Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) : version d’extension | Créer un abonnement Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’interrogation d’abonnements, consultez [Interroger les abonnements Event Grid](../query-event-subscriptions.md).
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
