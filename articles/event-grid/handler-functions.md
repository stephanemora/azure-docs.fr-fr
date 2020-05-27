---
title: Fonction Azure en tant que gestionnaire d’événements pour des événements Azure Event Grid
description: Décrit comment utiliser des fonctions Azure en tant que gestionnaires d’événements pour des événements Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 3ff3c0013cb7a373461b997b9922612763461b8d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595648"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Fonction Azure en tant que gestionnaire d’événements pour des événements Event Grid

Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue une action pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer des événements, et **Azure Functions** est l’un d’eux. 

Utilisez **Azure Functions** dans une architecture sans serveur pour répondre aux événements d’Event Grid. Quand vous utilisez une fonction Azure en tant que gestionnaire, utilisez le déclencheur Event Grid au lieu du déclencheur HTTP générique. Event Grid valide automatiquement les déclencheurs Event Grid. Dans le cas des déclencheurs HTTP génériques, vous devez implémenter vous-même la [réponse de validation](webhook-event-delivery.md).

Pour plus d’informations, consultez [Déclencheur Event Grid pour Azure Functions](../azure-functions/functions-bindings-event-grid.md) pour obtenir une vue d’ensemble de l’utilisation du déclencheur Event Grid dans les fonctions.

## <a name="tutorials"></a>Tutoriels

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Gérer les événements avec une fonction](custom-event-to-function.md) | Envoie un événement personnalisé à une fonction pour traitement. |
| [Tutoriel : Automatiser le redimensionnement des images chargées à l’aide d’Event Grid](resize-images-on-storage-blob-upload-event.md) | Les utilisateurs chargent les images par le biais de l’application web sur le compte de stockage. Quand un objet blob de stockage est créé, Event Grid envoie un événement à l’application de fonction, qui redimensionne l’image chargée. |
| [Tutoriel : Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md) | Quand Event Hubs crée un fichier Capture, Event Grid envoie un événement à une application de fonction. L’application récupère le fichier Capture et migre les données vers un entrepôt de données. |
| [Tutoriel : Exemples d’intégration d’Azure Service Bus et d’Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envoie des messages à partir de la rubrique Service Bus à une application de fonction et à une application logique. |


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 
