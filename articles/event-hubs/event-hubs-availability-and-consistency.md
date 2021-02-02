---
title: Disponibilité et cohérence - Azure Event Hubs | Microsoft Docs
description: Découvrez comment obtenir la quantité maximale de disponibilité et de cohérence avec Azure Event Hubs à l’aide de partitions.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882193"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilité et cohérence dans Event Hubs
Cet article fournit des informations sur la disponibilité et la cohérence prises en charge par Azure Event Hubs. 

## <a name="availability"></a>Disponibilité
Azure Event Hubs répartit le risque de défaillances catastrophiques d’ordinateurs individuels ou même de racks complets sur des clusters qui s’étendent sur plusieurs domaines d’échec au sein d’un centre de donnée. Il implémente des mécanismes transparents de détection des défaillances et de basculement, de sorte que le service continue à fonctionner dans les limites des niveaux de service assurés et, en général, sans interruption notable lorsque de telles défaillances se produisent. Si un espace de noms Event Hubs a été créé avec l’option [Zones de disponibilité](../availability-zones/az-overview.md) activée, le risque de panne est davantage réparti dans trois sites physiquement séparés. Le service dispose par ailleurs de réserves de capacité suffisantes pour faire face instantanément à la perte complète et irrémédiable de l’ensemble du site. Pour en savoir plus, consultez [Azure Event Hubs – Géo-reprise d’activité après sinistre](event-hubs-geo-dr.md).

Lorsqu’une application cliente envoie des événements à un Event Hub, les événements sont automatiquement distribués entre les partitions de votre Event Hub. Si une partition n’est pas disponible pour une raison quelconque, les événements sont distribués entre les partitions restantes. Ce comportement offre le meilleur temps d’activité. Pour les cas d’usage qui requièrent la durée maximale de bon fonctionnement, ce modèle est préférable à l’envoi des événements à une partition spécifique. Pour plus d'informations, consultez [Partitions](event-hubs-scalability.md#partitions).

## <a name="consistency"></a>Cohérence
Dans certains scénarios, l’ordre des événements peut être important. Par exemple, vous souhaiterez peut-être que votre système principal traite une commande de mise à jour avant une commande de suppression. Dans ce scénario, une application cliente envoie des événements à une partition spécifique afin que l’ordre soit conservé. Lorsqu’une application consommateur consomme ces événements à partir de la partition, ceux-ci sont lus dans l’ordre. 

Avec cette configuration, gardez à l’esprit que si la partition particulière à laquelle vous envoyez les événements n’est pas disponible, vous recevrez une réponse d’erreur. À titre de comparaison, si vous n’avez pas d’affinité avec une partition unique, le service Event Hubs envoie votre événement à la prochaine partition disponible.

Pour garantir le classement tout en optimisant aussi le temps d’activité, vous pouvez regrouper les événements dans le cadre de votre application de traitement des événements. Le moyen le plus simple d’y parvenir consiste à marquer votre événement avec une propriété de numéro de séquence personnalisée.

Dans ce scénario, le client producteur envoie des événements à l’une des partitions disponibles dans votre Event Hub et définit le numéro de séquence correspondant à partir de votre application. Cette solution nécessite que l’état soit conservé par votre application de traitement, mais offre à vos expéditeurs un point de terminaison plus susceptible d’être disponible.

## <a name="appendix"></a>Annexe

### <a name="net-examples"></a>Exemples .NET

#### <a name="send-events-to-a-specific-partition"></a>Envoyer des événements à une partition spécifique
Définissez la clé de partition sur un événement ou utilisez un objet `PartitionSender` (si vous utilisez l’ancienne bibliothèque Microsoft.Azure.Messaging) pour envoyer uniquement les événements à une certaine partition. Cela garantit que ces événements sont lus dans l’ordre lorsqu’ils sont lus à partir de la partition. 

Si vous utilisez la bibliothèque **Azure.Messaging.EventHubs** la plus récente, consultez [Migration du code de PartitionSender vers EventHubProducerClient pour la publication d’événements sur une partition](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (version 5.0.0 ou ultérieure)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (version 4.1.0 ou ultérieure)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>Définir un numéro de séquence
L’exemple suivant marque votre événement avec une propriété de numéro de séquence personnalisée. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure.Messaging.EventHubs (version 5.0.0 ou ultérieure)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft.Azure.EventHubs (version 4.1.0 ou ultérieure)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

L’exemple envoie votre événement à l’une des partitions disponibles dans votre concentrateur d’événements et définit le numéro de séquence correspondant à partir de votre application. Cette solution nécessite que l’état soit conservé par votre application de traitement, mais offre à vos expéditeurs un point de terminaison plus susceptible d’être disponible.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Présentation du service Event Hubs](./event-hubs-about.md)
* [Créer un concentrateur d’événements](event-hubs-create.md)
