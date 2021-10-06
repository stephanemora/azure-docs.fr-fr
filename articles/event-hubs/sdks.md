---
title: Azure Event Hubs - Kits de développement logiciel (SDK) client | Microsoft Docs
description: Cet article fournit des informations sur les kits de développement logiciel (SDK) clients pour Azure Event Hubs.
ms.topic: article
ms.date: 09/21/2021
ms.openlocfilehash: f3fd3407ae96cf2119bb97d2a47b647c33db9332
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623529"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs - Kits de développement logiciel (SDK)
Cet article fournit les informations suivantes pour les kits de développement logiciel (SDK) pris en charge par Azure Event Hubs : 

- Emplacement du package que vous pouvez utiliser dans vos applications 
- Emplacement GitHub où vous pouvez trouver le code source, les exemples, le fichier Lisez-moi, le journal des modifications, les problèmes signalés, et signaler de nouveaux problèmes 
- Liens vers les tutoriels de démarrage rapide 

## <a name="client-sdks"></a>Kits de développement logiciel (SDK) client
Le tableau suivant décrit tous les clients de runtime des Hubs d'événements Azure actuellement disponibles. Ces bibliothèques sont principalement destinées à **envoyer et recevoir des messages** d’un hub d’événements.

| Langage | Package | Informations de référence | 
| -------- | ------- | --------------- | 
| . .NET Standard | [Azure.Messaging.EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Didacticiel](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure.Messaging.EventHubs.Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Didacticiel](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Didacticiel](event-hubs-java-get-started-send.md)</li></ul> |
|      | [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob)</li><li>[Didacticiel](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Didacticiel](event-hubs-python-get-started-send.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Didacticiel](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [azure/event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Didacticiel](event-hubs-node-get-started-send.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Didacticiel](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-event-hubs-go)</li><li>[Didacticiel](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-event-hubs-c)</li><li>[Didacticiel](event-hubs-c-getstarted-send.md)</li></ul> |

Le tableau ci-dessous répertorie les anciens clients de runtime des Hubs d'événements Azure. Bien que ces packages puissent recevoir des correctifs de bogues critiques, ils ne sont pas en cours de développement. Nous vous recommandons d’utiliser les derniers kits de développement logiciel (SDK) figurant dans le tableau ci-dessus.

| Langage | Package | Informations de référence | 
| -------- | ------- | --------------- | 
| . .NET Standard  | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) (**héritage**) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Didacticiel](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) (**héritage**) | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Didacticiel](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) (**héritage**) |<ul><li>[Didacticiel](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
|   Java   | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(version héritée)** | <ul><li>[Emplacement GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Didacticiel](event-hubs-java-get-started-send.md)</li></ul> |

## <a name="management-sdks"></a>Kits SDK de gestion
Voici la liste des bibliothèques actuellement disponibles pour la gestion. Aucune de ces bibliothèques ne contient d’opérations de runtime. Elles sont uniquement destinées à la **gestion des entités Event Hubs**.

- [.NET Standard](/dotnet/api/microsoft.azure.management.eventhub)
- [Java](/java/api/com.microsoft.azure.management.eventhub)
- [Python](/python/api/azure-mgmt-eventhub)
- [JavaScript](/javascript/api/@azure/arm-eventhub/)


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](./event-hubs-about.md)
* [Créer un hub d’événements](event-hubs-create.md)
* [FAQ sur les hubs d’événements](event-hubs-faq.yml)
