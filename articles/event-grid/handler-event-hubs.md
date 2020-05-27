---
title: Event Hub en tant que gestionnaire d’événements pour les événements Azure Event Grid
description: Décrit comment utiliser des concentrateurs d’événements en tant que gestionnaires d’événements pour des événements Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: c9ce4e7da51005dcb06c9df420d80f4d2c7b93e9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596018"
---
# <a name="event-hub-as-an-event-handler-for-azure-event-grid-events"></a>Event Hub en tant que gestionnaire d’événements pour des événements Azure Event Grid
Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue une action pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer des événements, et **Azure Event Hubs** est l’un d’eux. 

Utilisez **Event Hubs** si votre solution reçoit des événements d’Event Grid plus vite qu’elle ne peut les traiter. Une fois que les événements se trouvent dans un concentrateur d’événements, votre application peut traiter ces événements selon sa propre planification. Vous pouvez mettre à l’échelle votre système de traitement des événements de façon à gérer les événements entrants.

## <a name="tutorials"></a>Tutoriels
Regardez les exemples suivants : 

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Acheminer des événements personnalisés vers Azure Event Hubs avec Azure CLI](custom-event-to-eventhub.md) | Envoie un événement personnalisé à un hub d’événements afin qu’il soit traité par une application. |
| [Modèle Resource Manager : Créer une rubrique personnalisée Event Grid et envoyer des événements à un concentrateur d’événements](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Modèle Resource Manager qui crée un abonnement pour une rubrique personnalisée. Il envoie les événements vers Azure Event Hubs. |

## <a name="message-properties"></a>Propriétés de message
Si vous utilisez un **concentrateur d’événements** en tant que gestionnaire d’événements pour des événements d’Event Grid, définissez les en-têtes de message suivants : 

| Nom de la propriété | Description |
| ------------- | ----------- | 
| aeg-subscription-name | Nom de l’abonnement aux événements. |
| aeg-delivery-count | <p>Nombre de tentatives effectuées pour l’événement.</p> <p>Exemple : "1"</p> |
| aeg-event-type | <p>Type de l’événement.</p><p> Exemple : « Microsoft.Storage.blobCreated »</p> | 
| aeg-metadata-version | <p>Version de métadonnées de l’événement.</p> <p>Exemple : "1".</p><p> Pour un **schéma d’événement Event Grid**, cette propriété représente la version des métadonnées et, pour un **schéma d’événements cloud**, elle représente la **version des spécifications**. </p>|
| aeg-data-version | <p>Version de données de l’événement.</p><p>Exemple : "1".</p><p>Pour un **schéma d’événement Event Grid**, cette propriété représente la version des données et, pour un **schéma d’événements cloud**, elle ne s’applique pas.</p> |
| aeg-output-event-id | ID de l’événement Event Grid. |


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 
