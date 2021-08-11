---
title: 'Tutoriel : Envoyer des données Event Hubs vers un entrepôt de données - Event Grid'
description: Décrit comment stocker des données Event Hubs capturées dans Azure Synapse Analytics à l’aide de déclencheurs Azure Functions et Event Grid.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2887cdc1b4666daf73a21a76907f80d50d345d82
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414003"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Tutoriel : Diffuser en continu des Big Data dans un entrepôt de données
Azure [Event Grid](overview.md) est un service de routage des événements intelligent qui vous permet de réagir aux notifications ou événements issus des applications et services. Par exemple, ce service peut déclencher le traitement par une fonction Azure des données Event Hubs capturées dans un Stockage Blob ou Data Lake Storage. Cet [exemple](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) vous montre comment utiliser Event Grid et Azure Functions pour migrer des données Event Hubs capturées d’un Stockage Blob vers Azure Synapse Analytics, plus précisément un pool SQL dédié.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](./includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différences entre les services de messagerie Azure, consultez [Choisir entre des services Azure qui remettent des messages](compare-messaging-services.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour découvrir Event Hubs Capture, consultez [Activer Event Hubs Capture à l’aide du portail Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Pour plus d’informations sur la configuration et l’exécution de l’exemple, consultez [Exemple Event Hubs Capture et Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
