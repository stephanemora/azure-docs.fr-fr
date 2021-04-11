---
title: Ajouter des données personnalisées à des événements dans Azure Event Hubs
description: Cet article vous montre comment ajouter des données personnalisées aux événements dans Azure Event Hubs.
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 3362b6ac4b0d624969aa3ba36d2ebc83b8777cf5
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104893397"
---
# <a name="add-custom-data-to-events-in-azure-event-hubs"></a>Ajouter des données personnalisées à des événements dans Azure Event Hubs
Étant donné qu’un événement se compose principalement d’un ensemble opaque d’octets, il peut être difficile pour les consommateurs de ces événements de prendre des décisions avisées sur la façon de les traiter. Pour permettre aux éditeurs d’événements d’offrir un meilleur contexte aux consommateurs, les événements peuvent également contenir des métadonnées personnalisées, sous la forme d’un ensemble de paires clé-valeur. Un scénario commun pour l’inclusion de métadonnées consiste à fournir une indication sur le type de données contenues par un événement, afin que les consommateurs comprennent son format et puissent le désérialiser de manière appropriée.

> [!NOTE]
> Ces métadonnées ne sont pas utilisées par le service Event Hubs, ou de quelque façon que ce soit. Elles existent uniquement pour la coordination entre les éditeurs d’événements et les consommateurs. 

Les sections suivantes vous montrent comment ajouter des données personnalisées à des événements dans différents langages de programmation. 

## <a name="net"></a>.NET 

```csharp
var eventBody = new BinaryData("Hello, Event Hubs!");
var eventData = new EventData(eventBody);
eventData.Properties.Add("EventType", "com.microsoft.samples.hello-event");
eventData.Properties.Add("priority", 1);
eventData.Properties.Add("score", 9.0);
```

Pour obtenir l’exemple de code complet, consultez [Publication d’événements avec des métadonnées personnalisées](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md#publishing-events-with-custom-metadata).

## <a name="java"></a>Java

```java
EventData firstEvent = new EventData("EventData Sample 1".getBytes(UTF_8));
firstEvent.getProperties().put("EventType", "com.microsoft.samples.hello-event");
firstEvent.getProperties().put("priority", 1);
firstEvent.getProperties().put("score", 9.0);
```

Pour obtenir l’exemple de code complet, consultez [publier des événements avec des métadonnées personnalisées](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/PublishEventsWithCustomMetadata.java).


## <a name="python"></a>Python

```python
event_data = EventData('Message with properties')
event_data.properties = {'event-type': 'com.microsoft.samples.hello-event', 'priority': 1, "score": 9.0}
```

Pour obtenir l’exemple de code complet, consultez [Envoyer un lot de données d’événements avec des propriétés](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="javascript"></a>JavaScript

```javascript
let eventData = { body: "First event", properties: { "event-type": "com.microsoft.samples.hello-event", "priority": 1, "score": 9.0  } };
```


## <a name="next-steps"></a>Étapes suivantes
Consultez les démarrages rapides et échantillons suivants. 

- Démarrages rapides : [.NET](event-hubs-dotnet-standard-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [JavaScript](event-hubs-node-get-started-send.md)
- Échantillons sur GitHub : [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples), [Python](https://github.com/Azure/azure-sdk-for-python/blob/azure-eventhub_5.3.1/sdk/eventhub/azure-eventhub/samples), [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript), [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
