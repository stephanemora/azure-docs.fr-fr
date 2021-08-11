---
title: Exemple de script Azure CLI - Créer une rubrique personnalisée | Microsoft Docs
description: Cet article fournit un exemple de script Azure CLI qui montre comment créer une rubrique personnalisée Azure Event Grid.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/22/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 54f56b1bce0c8d88bff3b39b6d4c8ff108106cb3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463778"
---
# <a name="create-event-grid-custom-topic-with-azure-cli"></a>Créer une rubrique personnalisée Event Grid avec Azure CLI

Ce script crée une rubrique personnalisée Event Grid.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/event-grid/create-custom-topic/create-custom-topic.sh "Create custom topic")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour créer la rubrique personnalisée. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az eventgrid topic create](/cli/azure/eventgrid/topic#az_eventgrid_topic_create) | Crée une rubrique personnalisée Event Grid. |


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’interrogation d’abonnements, consultez [Interroger les abonnements Event Grid](../query-event-subscriptions.md).
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).
