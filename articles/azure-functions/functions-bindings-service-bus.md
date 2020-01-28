---
title: Liaisons Azure Service Bus pour Azure Functions
description: Découvrez comment utiliser des déclencheurs et des liaisons Azure Service Bus dans Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 424d797410c091dc53687284c2b32e2f1f0358e1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549068"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Liaisons Azure Service Bus pour Azure Functions

Cet article explique comment utiliser des liaisons Azure Service Bus dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur et de sortie pour les files d’attente et les rubriques Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Les liaisons Service Bus sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus), version 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages – Functions 2.x et versions ultérieures

Les liaisons Service Bus sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus), version 3.x. Le code source pour le package se trouve dans le référentiel [azure-functions-servicebus-extension](https://github.com/Azure/azure-functions-servicebus-extension) GitHub.

> [!NOTE]
> Les versions 2.x et ultérieures ne créent pas la rubrique ni l’abonnement configurés dans l’instance `ServiceBusTrigger`. Ces versions sont basées sur [Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus), qui ne gère pas les files d’attente.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Déclencheur

Utilisez le déclencheur Service Bus pour répondre aux messages provenant d'une file d’attente ou d'une rubrique Service Bus. 

## <a name="trigger---example"></a>Déclencheur - exemple

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui lit les [métadonnées du message](#trigger---message-metadata) et consigne un message de la file d’attente Service Bus :

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de déclencheur Service Bus dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction lit [les métadonnées du message](#trigger---message-metadata) et consigne un message de la file d’attente Service Bus.

Voici les données de liaison dans le fichier *function.json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Voici le code Script C# :

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de déclencheur Service Bus dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction lit [les métadonnées du message](#trigger---message-metadata) et consigne un message de la file d’attente Service Bus. 

Voici les données de liaison dans le fichier *function.json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Voici le code de script JavaScript :

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre comment lire un message de file d’attente Service Bus via un déclencheur.

Une liaison Service Bus est définie dans *function.json*, où le *type* est défini sur `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Le code dans *_\_init_\_.py* déclare un paramètre `func.ServiceBusMessage` qui vous permet de lire le message de la file d’attente dans votre fonction.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

La fonction Java suivante utilise l’annotation `@ServiceBusQueueTrigger` provenant de la [bibliothèque runtime de fonctions Java](/java/api/overview/azure/functions/runtime) afin de décrire la configuration d’un déclencheur de file d’attente Service Bus. La fonction récupère le message placé dans la file d’attente et l’ajoute dans les journaux.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Les fonctions Java peuvent également être déclenchées lorsqu’un message est ajouté à une rubrique Service Bus. L’exemple suivant utilise l’annotation `@ServiceBusTopicTrigger` pour décrire la configuration du déclencheur.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="trigger---attributes-and-annotations"></a>Déclencheur – Attributs et annotations

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur de Service Bus :

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Le constructeur de l’attribut prend le nom de la file d’attente ou la rubrique et l’abonnement. Dans Azure Functions version 1.x, vous pouvez également spécifier les droits d’accès de la connexion. Si vous ne spécifiez pas de droits d’accès, la valeur par défaut est `Manage`. Pour en savoir plus, consultez la section [Déclencheur - configuration](#trigger---configuration).

  Voici un exemple montrant l’attribut utilisé avec un paramètre de chaîne :

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Vous pouvez définir la propriété `Connection` pour spécifier le nom d’un paramètre d’application contenant la chaîne de connexion Service Bus à utiliser, comme illustré dans l’exemple suivant :

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Pour obtenir un exemple complet, voir [Exemple de déclencheur](#trigger---example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Fournit une autre manière de spécifier le compte Service Bus à utiliser. Le constructeur prend le nom d’un paramètre d’application comportant une chaîne de connexion Service Bus. L’attribut peut être appliqué au niveau du paramètre, de la méthode ou de la classe. L’exemple suivant montre le niveau de la classe et celui de la méthode :

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Le compte Service Bus à utiliser est déterminé dans l’ordre suivant :

* La propriété `Connection` de l’attribut `ServiceBusTrigger`.
* L’attribut `ServiceBusAccount` appliqué au même paramètre que l’attribut `ServiceBusTrigger`.
* L’attribut `ServiceBusAccount` appliqué à la fonction.
* L’attribut `ServiceBusAccount` appliqué à la classe.
* Le paramètre d’application « AzureWebJobsServiceBus ».

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L’annotation `ServiceBusQueueTrigger` vous permet de créer une fonction qui s’exécute lors de la création d’un message de file d’attente Service Bus. Les options de configuration disponibles sont le nom de la file d’attente et le nom de la chaîne de connexion.

L’annotation `ServiceBusTopicTrigger` vous permet de désigner une rubrique et un abonnement pour cibler les données qui déclenchent la fonction.

Pour plus de détails, voir l’[exemple](#trigger---example) de déclencheur.

---

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `ServiceBusTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur « serviceBusTrigger ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur « in ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente le message de la file d’attente ou de la rubrique dans le code de la fonction. |
|**queueName**|**QueueName**|Nom de la file d’attente à surveiller.  Défini uniquement en cas de surveillance d’une file d’attente, ne s’applique pas à une rubrique.
|**topicName**|**TopicName**|Nom de la rubrique à surveiller. Défini uniquement en cas de surveillance d’une rubrique, ne s’applique pas à une file d’attente.|
|**subscriptionName**|**SubscriptionName**|Nom de l’abonnement à surveiller. Défini uniquement en cas de surveillance d’une rubrique, ne s’applique pas à une file d’attente.|
|**connection**|**Connection**|Nom d’un paramètre d’application comportant la chaîne de connexion Service Bus à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom. Par exemple, si vous définissez `connection` sur « MyServiceBus », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyServiceBus ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion Service Bus par défaut dans le paramètre d’application nommé « AzureWebJobsServiceBus ».<br><br>Pour obtenir une chaîne de connexion, suivez les étapes indiquées à la section [Obtenir les informations d’identification de gestion](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique. |
|**accessRights**|**y accéder**|Droits d’accès de la chaîne de connexion. Les valeurs disponibles sont `manage` et `listen`. La valeur par défaut est `manage`, ce qui indique que `connection` a l'autorisation **Gérer**. Si vous utilisez une chaîne de connexion qui n’a pas l'autorisation **Gérer**, définissez `accessRights` sur « écouter ». Sinon, le runtime Functions pourrait échouer à effectuer des opérations qui nécessitent des droits de gestion. Dans Azure Functions versions 2.x et ultérieures, cette propriété n’est pas disponible parce que la version la plus récente du Kit de développement logiciel (SDK) Service Bus ne prend pas en charge les opérations de gestion.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` en cas de connexion à une file d’attente ou à un abonnement [prenant en charge la session](../service-bus-messaging/message-sessions.md). Sinon `false`, qui est la valeur par défaut.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Déclencheur - utilisation

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Les types de paramètres suivants sont disponibles pour le message de la file d’attente ou de la rubrique :

* `string` -Si le message est un texte.
* `byte[]` - Utile pour les données binaires.
* Un type personnalisé - Si le message contient JSON, Azure Functions essaie de désérialiser les données JSON.
* `BrokeredMessage` - Vous donne le message désérialisé avec la méthode [BrokeredMessage.GetBody\<](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1).

Ces types de paramètres concernent Azure Functions version 1.x. Pour les versions 2.x et ultérieures, utilisez [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) au lieu de `BrokeredMessage`.

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les types de paramètres suivants sont disponibles pour le message de la file d’attente ou de la rubrique :

* `string` -Si le message est un texte.
* `byte[]` - Utile pour les données binaires.
* Un type personnalisé - Si le message contient JSON, Azure Functions essaie de désérialiser les données JSON.
* `BrokeredMessage` - Vous donne le message désérialisé avec la méthode [BrokeredMessage.GetBody\<](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1).

Ces paramètres concernent Azure Functions version 1.x ; pour 2.x et ultérieur, utilisez [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) au lieu de `BrokeredMessage`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Accédez au message de la file d’attente ou de la rubrique à l’aide de `context.bindings.<name from function.json>`. Le message Service Bus est passé à la fonction en tant que chaîne ou en tant qu’objet JSON.

# <a name="pythontabpython"></a>[Python](#tab/python)

Le message de la file d’attente est disponible pour la fonction via un paramètre de type `func.ServiceBusMessage`. Le message Service Bus est passé à la fonction en tant que chaîne ou en tant qu’objet JSON.

# <a name="javatabjava"></a>[Java](#tab/java)

Le message de Service Bus entrant est disponible via un paramètre `ServiceBusQueueMessage` ou `ServiceBusTopicMessage`.

[Pour plus de détails, voir les exemples](#trigger).

---

## <a name="trigger---poison-messages"></a>Déclencheur - messages incohérents

La gestion des messages incohérents ne peut pas être contrôlée ou configurée dans Azure Functions. Service Bus gère lui-même les messages incohérents.

## <a name="trigger---peeklock-behavior"></a>Déclencheur - Comportement de PeekLock

Le runtime Functions reçoit un message en [mode PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Il appelle l’élément `Complete` sur le message si la fonction se termine correctement. Si la fonction échoue, il appelle l’élément `Abandon`. Si la fonction s’exécute au-delà du délai imparti `PeekLock`, le verrou est automatiquement renouvelé tant que la fonction s’exécute. 

Le paramètre `maxAutoRenewDuration` peut être configuré dans *host.json*, qui mappe à [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). La valeur maximale autorisée pour ce paramètre est de 5 minutes, conformément à la documentation de Service Bus, tandis que vous pouvez augmenter la limite de temps de Functions jusqu’à 10 minutes (5 minutes par défaut). Pour les fonctions de Service Bus, vous ne voudrez pas le faire, car vous dépasseriez la limite de renouvellement de Service Bus.

## <a name="trigger---message-metadata"></a>Déclencheur - métadonnées de message

Le déclencheur Service Bus fournit plusieurs [propriétés de métadonnées](./functions-bindings-expressions-patterns.md#trigger-metadata). Ces propriétés peuvent être utilisées dans les expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Ce sont les propriétés de la classe [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).

|Propriété|Type|Description|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Le nombre de remises.|
|`DeadLetterSource`|`string`|La source de lettre morte.|
|`ExpiresAtUtc`|`DateTime`|Le délai d'expiration en UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Le temps de file d’attente en UTC.|
|`MessageId`|`string`|Valeur définie par l’utilisateur que Service Bus peut utiliser pour identifier les messages en double, si cette fonctionnalité est activée.|
|`ContentType`|`string`|Un identificateur de type de contenu utilisé par l’expéditeur et le récepteur pour une logique spécifique à l’application.|
|`ReplyTo`|`string`|L’adresse de file d’attente de réponse.|
|`SequenceNumber`|`Int64`|Le numéro unique attribué à un message par Service Bus.|
|`To`|`string`|L’adresse de destination.|
|`Label`|`string`|L’étiquette spécifique de l’application.|
|`CorrelationId`|`string`|L’ID de corrélation.|

> [!NOTE]
> Le déclencheur Service Bus qui fonctionne avec les abonnements et les files d’attente activées dans la session est actuellement en préversion. Veuillez suivre [cet élément](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) pour toute mise à jour supplémentaire concernant cette fonctionnalité. 

Consultez les [exemples de code](#trigger---example) qui utilisent ces propriétés précédemment dans cet article.

## <a name="output"></a>Output

Utilisez la liaison de sortie Azure Service Bus pour envoyer des messages de file d’attente ou de rubrique.

### <a name="output---example"></a>Sortie - exemple

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui envoie un message de la file d’attente Service Bus :

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise un déclencheur de minuteur pour envoyer un message de file d’attente toutes les 15 secondes.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Voici le code de script C# qui crée un message unique :

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Voici le code de script C# qui crée plusieurs messages :

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de sortie Service Bus dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction utilise un déclencheur de minuteur pour envoyer un message de file d’attente toutes les 15 secondes.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Voici le code de script JavaScript qui crée un message unique :

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Voici le code de script JavaScript qui crée plusieurs messages :

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre comment écrire dans une file d’attente Service Bus en Python.

Une définition de liaison Service Bus figure dans *function.json*, où *type* a la valeur `serviceBus`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

Dans *_\_init_\_.py*, vous pouvez écrire un message dans la file d’attente en passant une valeur à la méthode `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

L’exemple suivant montre une fonction Java qui envoie un message à une file d’attente Service Bus `myqueue` lorsqu’elle est déclenchée par une requête HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Dans la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@QueueOutput` sur les paramètres de fonction dont la valeur serait écrite dans une file d’attente Service Bus.  Le type de paramètre doit être `OutputBinding<T>`, où T désigne n’importe quel type Java natif d’un POJO.

Les fonctions Java peuvent également écrire dans une rubrique Service Bus. L’exemple suivant utilise l’annotation `@ServiceBusTopicOutput` afin de décrire la configuration pour la liaison de sortie. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="output---attributes-and-annotations"></a>Sortie – Attributs et annotations

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Le constructeur de l’attribut prend le nom de la file d’attente ou la rubrique et l’abonnement. Vous pouvez également spécifier les droits d’accès de la connexion. La détermination du paramètre des droits d’accès est expliquée dans la section [Sortie - configuration](#output---configuration). Voici un exemple qui illustre l’attribut appliqué à la valeur de retour de la fonction :

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le nom d’un paramètre d’application contenant la chaîne de connexion Service Bus à utiliser, comme illustré dans l’exemple suivant :

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, voir [Exemple de sortie](#output---example).

Vous pouvez utiliser l’attribut `ServiceBusAccount` pour spécifier le compte Service Bus à utiliser au niveau de la classe, de la méthode ou du paramètre.  Pour plus d’informations, consultez [Déclencheur - attributs](#trigger---attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Les annotations `ServiceBusQueueOutput` et `ServiceBusTopicOutput` sont disponibles pour écrire un message en tant que sortie de fonction. Le paramètre assorti de ces annotations doit être déclaré en tant que `OutputBinding<T>` où `T` est le type correspondant au type du message.

---

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `ServiceBus`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur « serviceBus ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Doit être défini sur « out ». Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente le message de la file d’attente ou de la rubrique dans le code de la fonction. Défini sur « $return » pour faire référence à la valeur de retour de la fonction. |
|**queueName**|**QueueName**|Nom de la file d’attente.  Défini uniquement en cas d’envoi de messages de file d’attente, ne s’applique pas à une rubrique.
|**topicName**|**TopicName**|Nom de la rubrique. Défini uniquement en cas d’envoi de messages de rubrique, ne s’applique pas à une file d’attente.|
|**connection**|**Connection**|Nom d’un paramètre d’application comportant la chaîne de connexion Service Bus à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom. Par exemple, si vous définissez `connection` sur « MyServiceBus », le runtime Functions recherche un paramètre d’application qui est nommé « AzureWebJobsMyServiceBus ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion Service Bus par défaut dans le paramètre d’application nommé « AzureWebJobsServiceBus ».<br><br>Pour obtenir une chaîne de connexion, suivez les étapes indiquées à la section [Obtenir les informations d’identification de gestion](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). La chaîne de connexion doit être destinée à un espace de noms Service Bus, et non limitée à une file d’attente ou une rubrique spécifique.|
|**accessRights**|**y accéder**|Droits d’accès de la chaîne de connexion. Les valeurs disponibles sont `manage` et `listen`. La valeur par défaut est `manage`, ce qui indique que `connection` a l'autorisation **Gérer**. Si vous utilisez une chaîne de connexion qui n’a pas l'autorisation **Gérer**, définissez `accessRights` sur « écouter ». Sinon, le runtime Functions pourrait échouer à effectuer des opérations qui nécessitent des droits de gestion. Dans Azure Functions versions 2.x et ultérieures, cette propriété n’est pas disponible parce que la version la plus récente du Kit de développement logiciel (SDK) Service Bus ne prend pas en charge les opérations de gestion.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

Dans Azure Functions version 1.x, le runtime crée la file d’attente si elle n’existe pas et si vous avez défini `accessRights` sur `manage`. Dans Functions version 2.x et ultérieur, la file d’attente ou la rubrique doit déjà exister. Si vous spécifiez une file d’attente ou une rubrique qui n’existe pas, la fonction échoue. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Utilisez les types de paramètres suivants pour la liaison de sortie :

* `out T paramName` - `T` peut être n’importe quel type sérialisable au format JSON. Si la valeur du paramètre est null lorsque la fonction se termine, Functions crée le message avec un objet null.
* `out string` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out byte[]` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out BrokeredMessage` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message (pour Functions 1.x).
* `out Message` - Si la valeur du paramètre est null quand la fonction se termine, Functions ne crée pas de message (pour Functions 2.x et ultérieur)
* `ICollector<T>` ou `IAsyncCollector<T>` - Pour la création de plusieurs messages. Un message est créé quand vous appelez la méthode `Add` .

Quand vous utilisez des fonctions C# :

* Les fonctions asynchrones ont besoin d’une valeur renvoyée ou de `IAsyncCollector` au lieu d’un paramètre `out`.

* Pour accéder à l’ID de session, établissez une liaison à un type [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) et utilisez la propriété `sessionId`.

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Utilisez les types de paramètres suivants pour la liaison de sortie :

* `out T paramName` - `T` peut être n’importe quel type sérialisable au format JSON. Si la valeur du paramètre est null lorsque la fonction se termine, Functions crée le message avec un objet null.
* `out string` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out byte[]` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message.
* `out BrokeredMessage` - Si la valeur du paramètre est null lorsque la fonction se termine, Functions ne crée pas de message (pour Functions 1.x).
* `out Message` - Si la valeur du paramètre est null quand la fonction se termine, Functions ne crée pas de message (pour Functions 2.x et ultérieur)
* `ICollector<T>` ou `IAsyncCollector<T>` - Pour la création de plusieurs messages. Un message est créé quand vous appelez la méthode `Add` .

Quand vous utilisez des fonctions C# :

* Les fonctions asynchrones ont besoin d’une valeur renvoyée ou de `IAsyncCollector` au lieu d’un paramètre `out`.

* Pour accéder à l’ID de session, établissez une liaison à un type [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) et utilisez la propriété `sessionId`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Accédez à la file d’attente ou la rubrique à l’aide de `context.bindings.<name from function.json>`. Vous pouvez affecter une chaîne, un tableau d’octets ou un objet Javascript (désérialisé au format JSON) à `context.binding.<name>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Utilisez le [Kit de développement logiciel (SDK) Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging) plutôt que la liaison de sortie intégrée.

# <a name="javatabjava"></a>[Java](#tab/java)

Utilisez le [Kit de développement logiciel (SDK) Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging) plutôt que la liaison de sortie intégrée.

---

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison | Informations de référence |
|---|---|
| Service Bus | [Codes d’erreur de Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites de Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration globaux disponibles pour cette liaison dans les versions 2.x et ultérieures. L’exemple de fichier host.json ci-dessous contient seulement les paramètres pour cette liaison. Pour plus d’informations sur les paramètres de configuration globale, consultez [Informations de référence sur le fichier host.json pour la version d’Azure Functions](functions-host-json.md).

> [!NOTE]
> Pour obtenir une référence de host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Durée maximale pendant laquelle le verrouillage de message doit être renouvelé automatiquement.|
|autoComplete|true|Si le déclencheur doit immédiatement marquer le message comme complet (saisie semi-automatique) ou attendre que la fonction s’arrête correctement pour que l’appel soit complet.|
|maxConcurrentCalls|16|Nombre maximal d’appels simultanés pour le rappel que la pompe de messages doit initier. Par défaut, le runtime Functions traite plusieurs messages simultanément. Pour que le runtime ne traite qu’un message de file d’attente ou de rubrique à la fois, définissez `maxConcurrentCalls` sur 1. |

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
