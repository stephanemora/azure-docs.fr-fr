---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 27333f272ca5000fd3b09b305712875c065f6bc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924431"
---
## <a name="trigger"></a>Déclencheur

Utilisez le déclencheur de fonction pour répondre à un événement envoyé à un flux d’événements d’un hub d’événements. Vous devez disposer de l’accès en lecture au hub d’événements sous-jacent pour configurer le déclencheur. Une fois la fonction déclenchée, le message passé à la fonction est du type chaîne.

## <a name="trigger---scaling"></a>Déclencheur - mise à l'échelle

Chaque instance d’une fonction déclenchée par un événement est sauvegardée par une seule instance [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Le déclencheur (alimenté par Event Hubs) garantit qu’une seule instance [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) peut obtenir un bail sur une partition donnée.

Prenons, par exemple, le hub d’événements suivant :

* 10 partitions
* 1 000 événements répartis uniformément sur toutes les partitions, avec 100 messages dans chaque partition

Lorsque votre fonction est activée pour la première fois, il n’existe qu’une seule instance de cette fonction. Nous appellerons la première instance de fonction `Function_0`. La fonction `Function_0` comprend une seule instance de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) qui détient un bail sur les dix partitions. Cette instance lit les événements des partitions 0 à 9. À partir de là, l’un des événements suivants se produit :

* **Les nouvelles instances de fonction ne sont pas nécessaires** : `Function_0` est capable de traiter l’ensemble des 1 000 événements avant que la logique de mise à l’échelle Functions ne prenne effet. Dans ce cas, l’intégralité des 1 000 messages sont traités par `Function_0`.

* **Une instance de fonction supplémentaire est ajoutée** : la logique de mise à l’échelle Functions détermine que si `Function_0` a plus de messages qu’elle ne peut en traiter, une instance d’application de fonction (`Function_1`) est ajoutée. Cette nouvelle fonction a également une instance associée de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Comme les hubs d’événements sous-jacents détectent qu’une nouvelle instance de l’hôte tente de lire des messages, ils équilibrent la charge des partitions entre ses instances d’hôte. Par exemple, les partitions 0 à 4 peuvent être affectées à `Function_0`, et les partitions 5 à 9 à `Function_1`.

* **N instances de fonction supplémentaires sont ajoutées** : Si la logique de mise à l’échelle de Functions détermine que `Function_0` et `Function_1` ont plus de messages qu’elles ne peuvent en traiter, de nouvelles instances d’application de fonction `Functions_N` sont créées.  Des applications sont créées jusqu’au point où `N` est supérieur au nombre de partitions de hub d’événements. Dans notre exemple, Event Hubs équilibre la charge des partitions, en l’occurrence, sur les instances `Function_0`...`Functions_9`.

Quand Functions effectue une mise à l’échelle, `N` instances est un nombre supérieur à celui des partitions du hub d’événements. Ainsi, des instances [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) sont toujours disponibles pour obtenir des verrous sur les partitions, à mesure que celles-ci sont mises à disposition par les autres instances. Vous n’êtes facturé que pour les ressources utilisées lors de l’exécution de l’instance de la fonction. En d’autres mots, vous n’êtes pas facturé pour cet approvisionnement excessif.

Quand toutes les exécutions de fonction se terminent (avec ou sans erreurs), des points de contrôle sont ajoutés au compte de stockage associé. Une fois les points de contrôle correctement créés, les 1 000 messages ne sont plus jamais récupérés.

## <a name="trigger---example"></a>Déclencheur - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#trigger---c-example)
* [Script C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Déclencheur - exemple C#

L’exemple suivant illustre un code [de fonction C#](../articles/azure-functions/functions-dotnet-class-library.md) qui consigne le corps du message du déclencheur de hub d’événements.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Pour accéder aux [métadonnées d’événement](#trigger---event-metadata) en code de fonction, effectuez une liaison avec l’objet [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (nécessite une instruction using pour `Microsoft.Azure.EventHubs`). Vous pouvez également accéder aux mêmes propriétés à l’aide d’expressions de liaison dans la signature de la méthode.  L’exemple suivant montre deux façons d’obtenir les mêmes données :

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Pour recevoir des événements en lot, transformez `string` ou `EventData` en tableau.  

> [!NOTE]
> Lors de la réception dans un lot, vous ne pouvez pas établir de liaison avec les paramètres de la méthode comme dans l’exemple ci-dessus avec `DateTime enqueuedTimeUtc` et vous devez les recevoir de chaque objet `EventData`.  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>Déclencheur - exemple Script C#

L’exemple suivant illustre une liaison de déclencheur de hub d’événements dans un fichier *function.json* et une [fonction de script C#](../articles/azure-functions/functions-reference-csharp.md) qui utilise la liaison. La fonction consigne le corps du message du déclencheur de hub d’événements.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*.

#### <a name="version-2x-and-higher"></a>Versions 2.x et ultérieures

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Voici le code Script C# :

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Pour accéder aux [métadonnées d’événement](#trigger---event-metadata) en code de fonction, effectuez une liaison avec l’objet [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (nécessite une instruction using pour `Microsoft.Azure.EventHubs`). Vous pouvez également accéder aux mêmes propriétés à l’aide d’expressions de liaison dans la signature de la méthode.  L’exemple suivant montre deux façons d’obtenir les mêmes données :

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Pour recevoir des événements en lot, transformez `string` ou `EventData` en tableau :

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Déclencheur - exemple F#

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction F#](../articles/azure-functions/functions-reference-fsharp.md) qui utilise la liaison. La fonction consigne le corps du message du déclencheur de hub d’événements.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*. 

#### <a name="version-2x-and-higher"></a>Versions 2.x et ultérieures

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Voici le code F# :

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Déclencheur - exemple JavaScript

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction JavaScript](../articles/azure-functions/functions-reference-node.md) qui utilise la liaison. La fonction lit les [métadonnées d’événement](#trigger---event-metadata) et enregistre le message.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*.

#### <a name="version-2x-and-higher"></a>Versions 2.x et ultérieures

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Pour recevoir les événements dans un lot, définissez `cardinality` sur `many` dans le fichier *function.json*, comme le montrent les exemples suivants.

#### <a name="version-2x-and-higher"></a>Versions 2.x et ultérieures

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Version 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>Déclencheur - Exemple Python

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction Python](../articles/azure-functions/functions-reference-python.md) qui utilise la liaison. La fonction lit les [métadonnées d’événement](#trigger---event-metadata) et enregistre le message.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Voici le code Python :

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Déclencheur - exemple Java

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction Java](../articles/azure-functions/functions-reference-java.md) qui utilise la liaison. La fonction consigne le corps du message du déclencheur Event Hub.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `EventHubTrigger` sur les paramètres dont la valeur proviendrait d’Event Hub. Les paramètres ayant ces annotations entraînent l’exécution de la fonction quand un événement se produit.  Vous pouvez utiliser cette annotation avec des types Java natifs, des objets POJO ou des valeurs Null avec Optional\<T>.

## <a name="trigger---attributes"></a>Déclencheur - attributs

Dans les [bibliothèques de classes C#](../articles/azure-functions/functions-dotnet-class-library.md), utilisez l’attribut [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs).

Le constructeur de l’attribut prend le nom du hub d’événements, le nom du groupe de consommateurs et le nom d’un paramètre d’application qui contient la chaîne de connexion. Pour plus d’informations sur ces paramètres, consultez la [section de configuration du déclencheur](#trigger---configuration). Voici un exemple d’attribut `EventHubTriggerAttribute` :

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `EventHubTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `eventHubTrigger`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente l’élément d’événement dans le code de la fonction. |
|**path** |**EventHubName** | Functions 1.x uniquement. Nom du hub d’événements. Lorsque le nom d’Event Hub est également présent dans la chaîne de connexion, sa valeur remplace cette propriété lors de l’exécution. |
|**eventHubName** |**EventHubName** | Functions 2.x et versions ultérieures. Nom du hub d’événements. Lorsque le nom d’Event Hub est également présent dans la chaîne de connexion, sa valeur remplace cette propriété lors de l’exécution. |
|**consumerGroup** |**ConsumerGroup** | Propriété facultative qui définit le [groupe de consommateurs](../articles/event-hubs/event-hubs-features.md#event-consumers) utilisé pour l’abonnement à des événements dans le hub. En cas d’omission, le groupe de consommateurs `$Default` est utilisé. |
|**cardinalité** | n/a | Pour Javascript. Définissez sur `many` afin d’activer le traitement par lot.  Si omis ou défini sur `one`, un message unique est transmis à la fonction. |
|**Connexion** |**Connection** | Le nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms du hub d’événements. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour [l’espace de noms](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), et non pour le hub d’événements lui-même. Cette chaîne de connexion doit avoir au moins des droits de lecture pour activer le déclencheur.|
|**path**|**EventHubName**|Nom du hub d’événements. Peut être référencé via les paramètres d’application `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Déclencheur - métadonnées d’événement

Le déclencheur Event Hubs fournit plusieurs [propriétés de métadonnées](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Ces propriétés peuvent être utilisées dans les expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Ce sont les propriétés de la classe [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Propriété|Type|Description|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|L’instance `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Le temps de file d’attente en UTC.|
|`Offset`|`string`|Le décalage des données par rapport au flux de données de la partition Event Hub. Le décalage est une marque ou un identificateur pour un événement au sein du flux Event Hubs. L’identificateur est unique au sein d’une partition du flux Event Hubs.|
|`PartitionKey`|`string`|La partition vers laquelle les données d’événement doivent être envoyées.|
|`Properties`|`IDictionary<String,Object>`|Les propriétés d’utilisateur pour les données d’événements.|
|`SequenceNumber`|`Int64`|Le numéro de séquence logique de l’événement.|
|`SystemProperties`|`IDictionary<String,Object>`|Les propriétés système, y compris les données d’événement.|

Consultez les [exemples de code](#trigger---example) qui utilisent ces propriétés précédemment dans cet article.

## <a name="trigger---hostjson-properties"></a>Déclencheur - propriétés de host.json

Le fichier [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contient les paramètres qui contrôlent le comportement du déclencheur Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Output

Utilisez la liaison de sortie Event Hubs pour écrire des événements dans un flux d’événements du hub d’événements. Vous devez disposer de l’autorisation d’envoi à un hub d’événements pour y écrire les événements.

Vérifiez que les références de package nécessaires sont en place avant d’essayer d’implémenter une liaison de sortie.

## <a name="output---example"></a>Sortie - exemple

Consultez l’exemple propre à un langage particulier :

* [C#](#output---c-example)
* [Script C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Sortie - exemple C#

L’exemple suivant illustre une [fonction C#](../articles/azure-functions/functions-dotnet-class-library.md) qui écrit un message dans un hub d’événements, en utilisant la valeur retournée par la méthode comme sortie :

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

L’exemple suivant montre comment utiliser l’interface `IAsyncCollector` pour envoyer un lot de messages. Ce scénario est courant lorsque vous traitez des messages provenant d’un hub d’événements et que vous envoyez le résultat à un autre hub d’événements.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

### <a name="output---c-script-example"></a>Sortie - exemple Script C#

L’exemple suivant illustre une liaison de déclencheur de hub d’événements dans un fichier *function.json* et une [fonction de script C#](../articles/azure-functions/functions-reference-csharp.md) qui utilise la liaison. La fonction écrit un message dans un hub d’événements.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*. Le premier exemple concerne Functions 2.x et versions ultérieures, tandis que le second concerne Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Voici le code Script C# qui crée un message :

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Voici le code de script C# qui crée plusieurs messages :

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Sortie - exemple F#

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction F#](../articles/azure-functions/functions-reference-fsharp.md) qui utilise la liaison. La fonction écrit un message dans un hub d’événements.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*. Le premier exemple concerne Functions 2.x et versions ultérieures, tandis que le second concerne Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Voici le code F# :

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Sortie - exemple JavaScript

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction JavaScript](../articles/azure-functions/functions-reference-node.md) qui utilise la liaison. La fonction écrit un message dans un hub d’événements.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*. Le premier exemple concerne Functions 2.x et versions ultérieures, tandis que le second concerne Functions 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Voici le code JavaScript qui envoie un message unique :

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Voici le code JavaScript qui envoie plusieurs messages :

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Sortie - Exemple Python

L’exemple suivant illustre une liaison de déclencheur Event Hub dans un fichier *function.json* et une [fonction Python](../articles/azure-functions/functions-reference-python.md) qui utilise la liaison. La fonction écrit un message dans un hub d’événements.

Les exemples suivants illustrent les données de liaison Event Hubs dans le fichier *function.json*.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Voici le code Python qui envoie un message unique :

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Sortie : exemple Java

L’exemple suivant illustre une fonction Java qui écrit un message contenant l’heure actuelle à l’attention d’un Event Hub.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@EventHubOutput` sur les paramètres dont la valeur serait publiée à l’attention d’Event Hub.  Le type de paramètre doit être `OutputBinding<T>`, où T désigne un POJO ou n’importe quel type Java natif.

## <a name="output---attributes"></a>Sortie - attributs

Dans les [bibliothèques de classes C#](../articles/azure-functions/functions-dotnet-class-library.md), utilisez l’attribut [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

Le constructeur de l’attribut prend le nom du hub d’événements, le hub d’événements et le nom d’un paramètre d’application qui contient la chaîne de connexion. Pour plus d’informations sur ces paramètres, consultez [Sortie - configuration](#output---configuration). Voici un exemple d’attribut `EventHub` :

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output---c-example).

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `EventHub`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur eventHub. |
|**direction** | n/a | Doit être défini sur « out ». Ce paramètre est défini automatiquement lorsque vous créez la liaison dans le portail Azure. |
|**name** | n/a | Nom de variable utilisé dans le code de la fonction qui représente l’événement. |
|**path** |**EventHubName** | Functions 1.x uniquement. Nom du hub d’événements. Lorsque le nom d’Event Hub est également présent dans la chaîne de connexion, sa valeur remplace cette propriété lors de l’exécution. |
|**eventHubName** |**EventHubName** | Functions 2.x et versions ultérieures. Nom du hub d’événements. Lorsque le nom d’Event Hub est également présent dans la chaîne de connexion, sa valeur remplace cette propriété lors de l’exécution. |
|**Connexion** |**Connection** | Le nom d’un paramètre d’application qui contient la chaîne de connexion à l’espace de noms du hub d’événements. Copiez cette chaîne de connexion en cliquant sur le bouton **Informations de connexion** pour *l’espace de noms*, et non pour le hub d’événements lui-même. Cette chaîne de connexion doit disposer d’autorisations d’envoi pour envoyer le message au flux d’événements.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

Dans C# et Script C#, envoyez des messages en utilisant un paramètre de méthode comme `out string paramName`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Pour écrire plusieurs messages, vous pouvez utiliser `ICollector<string>` ou `IAsyncCollector<string>` à la place de `out string`.

Dans JavaScript, accédez à l’événement de sortie à l’aide de `context.bindings.<name>`. `<name>` est la valeur spécifiée dans la propriété `name` de *function.json*.

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Event Hub | [Guide des opérations](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration globaux disponibles pour cette liaison dans les versions 2.x et ultérieures. L’exemple de fichier host.json ci-dessous contient seulement les paramètres des versions 2.x et ultérieures pour cette liaison. Pour plus d’informations sur les paramètres de configuration globaux dans les versions 2.x et ultérieures, consultez [Informations de référence sur le fichier host.json pour Azure Functions](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Pour obtenir des informations de référence sur le fichier host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Propriété  |Default | Description |
|---------|---------|---------|
|maxBatchSize|64|Nombre d’événements maximal reçu par boucle de réception.|
|prefetchCount|n/a|Valeur PrefetchCount par défaut qui est utilisée par l’instance EventProcessorHost sous-jacente.|
|batchCheckpointFrequency|1|Nombre de lots d’événements à traiter avant de créer un point de contrôle de curseur EventHub.|
