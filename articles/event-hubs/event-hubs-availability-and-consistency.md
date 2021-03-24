---
title: Disponibilité et cohérence - Azure Event Hubs | Microsoft Docs
description: Découvrez comment obtenir la quantité maximale de disponibilité et de cohérence avec Azure Event Hubs à l’aide de partitions.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e014a33e94fe7f90569dd2ef1e9b620eef274842
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952862"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilité et cohérence dans Event Hubs
Cet article fournit des informations sur la disponibilité et la cohérence prises en charge par Azure Event Hubs. 

## <a name="availability"></a>Disponibilité
Azure Event Hubs répartit le risque de défaillances catastrophiques d’ordinateurs individuels ou même de racks complets sur des clusters qui s’étendent sur plusieurs domaines d’échec au sein d’un centre de donnée. Il implémente des mécanismes transparents de détection des défaillances et de basculement, de sorte que le service continue à fonctionner dans les limites des niveaux de service assurés et, en général, sans interruption notable lorsque de telles défaillances se produisent. 

Si un espace de noms Event Hubs a été créé avec les [zones de disponibilité](../availability-zones/az-overview.md) activées, le risque de panne est davantage réparti dans trois sites physiquement séparés. Le service dispose par ailleurs de réserves de capacité suffisantes pour faire face instantanément à la perte complète et irrémédiable de l’ensemble du site. Pour en savoir plus, consultez [Azure Event Hubs – Géo-reprise d’activité après sinistre](event-hubs-geo-dr.md).

Lorsqu’une application cliente envoie des événements à un hub d’événements sans spécifier de partition, les événements sont automatiquement distribués entre les partitions de votre hub d’événements. Si une partition n’est pas disponible pour une raison quelconque, les événements sont distribués entre les partitions restantes. Ce comportement offre le meilleur temps d’activité. Pour les cas d’usage qui requièrent la durée maximale de bon fonctionnement, ce modèle est préférable à l’envoi des événements à une partition spécifique. 

## <a name="consistency"></a>Cohérence
Dans certains scénarios, l’ordre des événements peut être important. Par exemple, vous souhaiterez peut-être que votre système principal traite une commande de mise à jour avant une commande de suppression. Dans ce scénario, une application cliente envoie des événements à une partition spécifique afin que l’ordre soit conservé. Lorsqu’une application consommateur consomme ces événements à partir de la partition, ceux-ci sont lus dans l’ordre. 

Avec cette configuration, gardez à l’esprit que si la partition particulière à laquelle vous envoyez les événements n’est pas disponible, vous recevrez une réponse d’erreur. À titre de comparaison, si vous n’avez pas d’affinité avec une partition unique, le service Event Hubs envoie votre événement à la prochaine partition disponible.

Par conséquent, si la haute disponibilité est la plus importante, ne ciblez pas une partition spécifique (à l’aide de l’ID/clé de partition). L’utilisation d’un ID/clé de partition rétrograde la disponibilité d’un hub d’événements au niveau de la partition. Dans ce scénario, vous effectuez un choix explicite entre la disponibilité (aucun ID/clé de partition) et la cohérence (épinglage d’événements à une partition spécifique). Pour plus d’informations sur les partitions dans Event Hubs, consultez [Partitions](event-hubs-features.md#partitions).

## <a name="appendix"></a>Annexe

### <a name="send-events-without-specifying-a-partition"></a>Envoyer des événements sans spécifier de partition
Nous vous recommandons d’envoyer des événements à un Event Hub sans définir des informations de partition pour permettre au service Event Hubs d’équilibrer la charge entre les partitions. Consultez les Démarrages rapides suivants pour savoir comment procéder dans différents langages de programmation. 

- [Envoyer des événements à l’aide de .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Envoyer les événements à l’aide de Java](event-hubs-java-get-started-send.md)
- [Envoyer les événements à l’aide de JavaScript](event-hubs-python-get-started-send.md)
- [Envoyer des événements à l’aide de Python](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Envoyer des événements à une partition spécifique
Dans cette section, vous allez apprendre à envoyer des événements à une partition spécifique à l’aide de différents langages de programmation. 

### <a name="net"></a>[.NET](#tab/dotnet)
Pour envoyer des événements à une partition spécifique, créez le lot à l’aide de la méthode [EventHubProducerClient.CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) en spécifiant le `PartitionId` ou le `PartitionKey` dans [CreateBatchOptions](/dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). Le code suivant envoie un lot d’événements à une partition spécifique en spécifiant une clé de partition. Azure Event Hubs garantit que tous les événements qui partagent une clé de partition sont stockés ensemble et livrés dans l’ordre d’arrivée.

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

Vous pouvez également utiliser la méthode [EventHubProducerClient.SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) en spécifiant **PartitionID** ou **PartitionKey** dans [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions).

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Pour envoyer des événements à une partition spécifique, créez le lot à l’aide de la méthode [createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) en spécifiant l’**ID de partition** ou la **clé de partition** dans [createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions). Le code suivant envoie un lot d’événements à une partition spécifique en spécifiant une clé de partition. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

Vous pouvez également utiliser la méthode [EventHubProducerClient.send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) en spécifiant l’**ID de partition** ou la **clé de partition** dans [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions).

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Pour envoyer des événements à une partition spécifique, lors de la création d’un lot à l’aide de la méthode [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-), spécifiez le `partition_id` ou le `partition_key`. Ensuite, utilisez la méthode [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) pour envoyer le lot à la partition du hub d’événements. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Vous pouvez également utiliser la méthode [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) en spécifiant des valeurs pour les paramètres `partition_id` ou `partition_key`.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Pour envoyer des événements à une partition spécifique, [créez un lot](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) à l’aide de l’objet [EventHubProducerClient.CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) en spécifiant le `partitionId` ou le `partitionKey`. Ensuite, envoyez le lot au hub d’événements à l’aide de la méthode [EventHubProducerClient.SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_). 

Consultez l’exemple qui suit.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

Vous pouvez également utiliser la méthode [EventHubProducerClient.sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) en spécifiant l’**ID de partition** ou la **clé de partition** dans [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions).

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

- [Présentation du service Event Hubs](./event-hubs-about.md)
- [Terminologie Event Hubs](event-hubs-features.md)
