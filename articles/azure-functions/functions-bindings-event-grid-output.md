---
title: Liaison de sortie Azure Event Grid pour Azure Functions
description: Découvrez comment envoyer un événement Event Grid dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97094674"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Liaison de sortie Azure Event Grid pour Azure Functions

Utilisez la liaison de sortie Event Grid pour écrire des événements dans une rubrique personnalisée. Vous devez avoir une [clé d’accès valide pour la rubrique personnalisée](../event-grid/security-authenticate-publishing-clients.md).

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](./functions-bindings-event-grid.md).

> [!NOTE]
> La liaison de sortie Event Grid ne prend pas en charge les signatures d’accès partagé (jetons SAS). Vous devez utiliser la clé d’accès de la rubrique.

> [!IMPORTANT]
> La liaison de sortie Event Grid est disponible seulement pour Functions 2.x et ultérieur.

## <a name="example"></a>Exemple

# <a name="c"></a>[C#](#tab/csharp)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui écrit un message dans une rubrique personnalisée Event Grid, en utilisant la valeur retournée par la méthode comme sortie :

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

L’exemple suivant montre comment utiliser l’interface `IAsyncCollector` pour envoyer un lot de messages.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre les données de la liaison de sortie Event Grid dans le fichier *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Voici le code du script C# qui crée un événement :

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Voici le code du script C# qui crée plusieurs événements :

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="java"></a>[Java](#tab/java)

La liaison de sortie Event Grid n’est pas disponible pour Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre les données de la liaison de sortie Event Grid dans le fichier *function.json*.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Voici le code du script JavaScript qui crée un événement :

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Voici le code du script JavaScript qui crée plusieurs événements :

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

L’exemple suivant montre comment configurer une fonction pour générer un message d’événement Event Grid. La section dans laquelle `type` a la valeur `eventGrid` configure les valeurs nécessaires pour établir une liaison de sortie Event Grid.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

Dans votre fonction, utilisez `Push-OutputBinding` pour envoyer un événement à une rubrique personnalisée par le biais de la liaison de sortie Event Grid.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

L’exemple suivant montre une liaison de déclencheur dans un fichier *function.json* et une [fonction Python](functions-reference-python.md) qui utilise la liaison. Il envoie ensuite un événement à la rubrique personnalisée, comme spécifié par `topicEndpointUri`.

Voici les données de liaison dans le fichier *function.json* :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Voici l’exemple Python pour envoyer un événement à une rubrique personnalisée en définissant `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs).

Le constructeur de l’attribut prend le nom d’un paramètre d’application qui contient le nom de la rubrique et le nom d’un paramètre d’application qui contient la clé de la rubrique. Pour plus d’informations sur ces paramètres, consultez [Sortie - configuration](#configuration). Voici un exemple d’attribut `EventGrid` :

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [exemple](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="java"></a>[Java](#tab/java)

La liaison de sortie Event Grid n’est pas disponible pour Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Les attributs ne sont pas pris en charge par PowerShell.

# <a name="python"></a>[Python](#tab/python)

La liaison de sortie Event Grid n’est pas disponible pour Python.

---

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `EventGrid`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur « eventGrid ». |
|**direction** | n/a | Doit être défini sur « out ». Ce paramètre est défini automatiquement lorsque vous créez la liaison dans le portail Azure. |
|**name** | n/a | Nom de variable utilisé dans le code de la fonction qui représente l’événement. |
|**topicEndpointUri** |**TopicEndpointUri** | Nom d’un paramètre d’application qui contient l’URI pour la rubrique personnalisée, comme `MyTopicEndpointUri`. |
|**topicKeySetting** |**TopicKeySetting** | Nom d’un paramètre d’application qui contient une clé d’accès pour la rubrique personnalisée. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Veillez à définir la valeur de la propriété de configuration `TopicEndpointUri` sur le nom d’un paramètre d’application qui contient l’URI de la rubrique personnalisée. Ne spécifiez pas l’URI de la rubrique personnalisée directement dans cette propriété.

## <a name="usage"></a>Usage

# <a name="c"></a>[C#](#tab/csharp)

Envoyez des messages en utilisant un paramètre de méthode comme `out EventGridEvent paramName`. Pour écrire plusieurs messages, vous pouvez utiliser `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` à la place de `out EventGridEvent`.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Envoyez des messages en utilisant un paramètre de méthode comme `out EventGridEvent paramName`. Dans Script C#, `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Pour écrire plusieurs messages, vous pouvez utiliser `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` à la place de `out EventGridEvent`.

# <a name="java"></a>[Java](#tab/java)

La liaison de sortie Event Grid n’est pas disponible pour Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Accédez à l’événement de sortie à l’aide de `context.bindings.<name>` où `<name>` est la valeur spécifiée dans la propriété `name` de *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Accédez à l’événement de sortie avec la cmdlet `Push-OutputBinding` pour envoyer un événement à la liaison de sortie Event Grid.

# <a name="python"></a>[Python](#tab/python)

La liaison de sortie Event Grid n’est pas disponible pour Python.

---

## <a name="next-steps"></a>Étapes suivantes

* [Distribuer un événement Event Grid](./functions-bindings-event-grid-trigger.md)
