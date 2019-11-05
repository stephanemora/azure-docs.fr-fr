---
title: Exemple de script Azure CLI - Créer une rubrique personnalisée | Microsoft Docs
description: Exemple de script Azure CLI - Créer une rubrique personnalisée
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
ms.openlocfilehash: 16c6586939a6dad248cef3abdabd78faf04a2381
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549269"
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
| [az eventgrid topic create](https://docs.microsoft.com/cli/azure/eventgrid/topic#az-eventgrid-topic-create) | Crée une rubrique personnalisée Event Grid. |


## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’interrogation d’abonnements, consultez [Interroger les abonnements Event Grid](../query-event-subscriptions.md).
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).
