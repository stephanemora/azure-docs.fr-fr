---
title: Exemple de script Azure CLI - S’abonner à un compte de stockage blob | Microsoft Docs
description: Exemple de script Azure CLI - S’abonner à un compte de stockage blob
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
ms.openlocfilehash: c43c410c731dc46e9eda64bffcfdf49800653dd5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998214"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>S’abonner aux événements d’un compte de stockage blob avec Azure CLI

Ce script crée un abonnement Event Grid aux événements d’un compte de stockage blob. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise la commande suivante pour créer l’abonnement aux événements. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Créer un abonnement Event Grid. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) : version d’extension | Créer un abonnement Event Grid. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur l’interrogation d’abonnements, consultez [Interroger les abonnements Event Grid](../query-event-subscriptions.md).
* Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).