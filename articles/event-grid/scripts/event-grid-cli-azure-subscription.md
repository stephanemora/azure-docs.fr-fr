---
title: Exemple de script Azure CLI - S’abonner à un abonnement Azure | Microsoft Docs
description: Cet article fournit un exemple de script Azure CLI qui montre comment s’abonner à des événements Azure Event Grid avec Azure CLI.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe81be64ea4672d233564726cfdae5b60951057d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87494330"
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
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
