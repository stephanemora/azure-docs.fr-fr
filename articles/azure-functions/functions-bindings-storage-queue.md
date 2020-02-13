---
title: Liaisons de stockage File d’attente Azure pour Azure Functions
description: Comprendre comment utiliser la liaison de sortie et le déclencheur Stockage File d’attente Azure dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 33bdf718e74011dbd7adedd766ebc90923fffb83
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189836"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Liaisons de stockage File d’attente Azure pour Azure Functions

Cet article explique comment utiliser les liaisons Stockage File d’attente Azure dans Azure Functions. Azure Functions prend en charge les liaisons de déclencheur et de sortie pour les files d’attente.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Les liaisons de Stockage File d’attente sont fournies dans le package NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), version 2.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages – Functions 2.x et versions ultérieures

Les liaisons de Stockage File d’attente sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), version 3.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encodage
Les fonctions attendent une chaîne codée en *base64*. Tout ajustement du type d’encodage (afin de préparer les données sous forme de chaîne codée en *base64*) doit être effectué dans le service appelant.

## <a name="trigger"></a>Déclencheur

Utilisez le déclencheur de file d’attente pour démarrer une fonction lorsqu’un nouvel élément est reçu sur une file d’attente. Le message de file d’attente est fourni comme entrée pour la fonction.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’exemple suivant montre un code de [fonction C#](functions-dotnet-class-library.md) qui interroge la file d’attente `myqueue-items` et écrit un journal chaque fois qu’un élément de la file d’attente est traité.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de déclencheur de file d’attente dans un fichier *function.json*, ainsi que le code du [script C# (.csx)](functions-reference-csharp.md) qui utilise cette liaison. La fonction interroge la file d’attente `myqueue-items` et écrit un journal chaque fois qu’un élément de la file d’attente est traité.

Voici le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

La section [utilisation](#trigger---usage) explique `myQueueItem`, qui est nommé par la propriété `name` dans function.json.  La [section sur les métadonnées de message](#trigger---message-metadata) détaille toutes les autres variables indiquées.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de déclencheur de file d’attente dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction interroge la file d’attente `myqueue-items` et écrit un journal chaque fois qu’un élément de la file d’attente est traité.

Voici le fichier *function.json* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

La section [configuration](#trigger---configuration) décrit ces propriétés.

> [!NOTE]
> Le paramètre de nom se reflète en tant que `context.bindings.<name>` dans le code JavaScript qui contient la charge utile de l’élément de file d’attente. Cette charge utile est également passée comme deuxième paramètre à la fonction.

Voici le code JavaScript :

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

La section [utilisation](#trigger---usage) explique `myQueueItem`, qui est nommé par la propriété `name` dans function.json.  La [section sur les métadonnées de message](#trigger---message-metadata) détaille toutes les autres variables indiquées.

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre comment lire un message de file d’attente passé à une fonction via un déclencheur.

Un déclencheur de file d’attente de stockage est défini dans *function.json*, où *Type* est défini sur `queueTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Le code dans *_\_init_\_.py* déclare un paramètre `func.ServiceBusMessage` qui vous permet de lire le message de la file d’attente dans votre fonction.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

L’exemple Java suivant montre une fonction de déclencheur de file d’attente de stockage, qui consigne le message déclenché placé dans la file d’attente `myqueuename`.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="trigger---attributes-and-annotations"></a>Déclencheur - Attributs et annotations

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez les attributs suivants pour configurer un déclencheur de file d’attente :

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Le constructeur de l’attribut prend le nom de la file d’attente à surveiller, comme illustré dans l’exemple suivant :

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Vous pouvez définir la propriété `Connection` pour spécifier le paramètre d’application qui contient la chaîne de connexion du compte de stockage à utiliser, comme illustré dans l’exemple suivant :

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Pour obtenir un exemple complet, consultez [Déclencheur - exemple C#](#trigger).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fournit une autre manière de spécifier le compte de stockage à utiliser. Le constructeur prend le nom d’un paramètre d’application comportant une chaîne de connexion de stockage. L’attribut peut être appliqué au niveau du paramètre, de la méthode ou de la classe. L’exemple suivant montre le niveau de la classe et celui de la méthode :

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Le compte de stockage à utiliser est déterminé dans l’ordre suivant :

* La propriété `Connection` de l’attribut `QueueTrigger`.
* L’attribut `StorageAccount` appliqué au même paramètre que l’attribut `QueueTrigger`.
* L’attribut `StorageAccount` appliqué à la fonction.
* L’attribut `StorageAccount` appliqué à la classe.
* Le paramètre d’application « AzureWebJobsStorage ».

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L’annotation `QueueTrigger` vous donne accès à la file d’attente qui déclenche la fonction. L’exemple suivant rend le message de file d’attente disponible pour la fonction via le paramètre `message`.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Propriété    | Description |
|-------------|-----------------------------|
|`name`       | Déclare le nom du paramètre dans la signature de la fonction. Quand la fonction est déclenchée, la valeur de ce paramètre a le contenu du message de la file d’attente. |
|`queueName`  | Déclare le nom de la file d’attente dans le compte de stockage. |
|`connection` | Pointe vers la chaîne de connexion du compte de stockage. |

---

## <a name="trigger---configuration"></a>Déclencheur - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `QueueTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a| Cette propriété doit être définie sur `queueTrigger`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction**| n/a | Dans le fichier *function.json* uniquement. Cette propriété doit être définie sur `in`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a |Nom de la variable qui contient la charge utile de l’élément de file d’attente dans le code de fonction.  |
|**queueName** | **QueueName**| Nom de la file d’attente à interroger. |
|**connection** | **Connection** |Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « MyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Déclencheur - utilisation

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Accédez aux données du message en utilisant un paramètre de méthode comme `string paramName`. Vous pouvez lier aux types suivants :

* Objet : le runtime Functions désérialise une charge utile JSON dans une instance d’une classe arbitraire définie dans votre code. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Si vous essayez de lier à `CloudQueueMessage` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Accédez aux données du message en utilisant un paramètre de méthode comme `string paramName`. `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Vous pouvez lier aux types suivants :

* Objet : le runtime Functions désérialise une charge utile JSON dans une instance d’une classe arbitraire définie dans votre code. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Si vous essayez de lier à `CloudQueueMessage` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

La charge utile de l’élément de la file d’attente est disponible via `context.bindings.<NAME>`, où `<NAME>` correspond au nom défini dans *function.json*. Si la charge utile est JSON, la valeur est désérialisée en objet.

# <a name="pythontabpython"></a>[Python](#tab/python)

Accédez au message de la file d’attente via le paramètre typé en [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="javatabjava"></a>[Java](#tab/java)

L’annotation [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) vous donne accès au message de la file d’attente qui a déclenché la fonction.

---

## <a name="trigger---message-metadata"></a>Déclencheur - métadonnées de message

Le déclencheur de file d’attente fournit plusieurs [propriétés de métadonnées](./functions-bindings-expressions-patterns.md#trigger-metadata). Ces propriétés peuvent être utilisées dans les expressions de liaison dans d’autres liaisons ou en tant que paramètres dans votre code. Ce sont les propriétés de la classe [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage).

|Propriété|Type|Description|
|--------|----|-----------|
|`QueueTrigger`|`string`|Charge utile de file d’attente (s’il s’agit d’une chaîne valide). Si la charge utile du message de la file d’attente est une chaîne, `QueueTrigger` a la même valeur que la variable nommée par la propriété `name` dans *function.json*.|
|`DequeueCount`|`int`|Nombre de fois que ce message a été enlevé de la file d’attente.|
|`ExpirationTime`|`DateTimeOffset`|Heure à laquelle le message expire.|
|`Id`|`string`|ID de message de la file d’attente.|
|`InsertionTime`|`DateTimeOffset`|Heure à laquelle le message a été ajouté à la file d’attente.|
|`NextVisibleTime`|`DateTimeOffset`|Heure à laquelle le message sera de nouveau visible.|
|`PopReceipt`|`string`|Réception pop du message.|

## <a name="trigger---poison-messages"></a>Déclencheur - messages incohérents

En cas d’échec d’une fonction de déclenchement de file d’attente, Azure Functions réessaie la fonction jusqu’à cinq fois (première tentative comprise) pour un message de file d’attente donné. Si les cinq tentatives échouent, le runtime Functions ajoute un message à une file d’attente nommée *&lt;nom_file_d’attente_d’origine>-poison*. Vous pouvez écrire une fonction pour traiter les messages de la file d’attente de messages incohérents en les consignant dans un journal ou en envoyant une notification signalant qu’une attention manuelle est nécessaire.

Pour gérer manuellement les messages incohérents, vérifiez la propriété [dequeueCount](#trigger---message-metadata) dans le message de file d’attente.

## <a name="trigger---polling-algorithm"></a>Déclencheur : algorithme d’interrogation

Le déclencheur de file d’attente implémente un algorithme d’interruption exponentiel et aléatoire pour réduire l’effet de l’interrogation de file d’attente inactive sur les coûts de transactions de stockage.

L’algorithme utilise la logique suivante :

- Lorsqu’un message est trouvé, le runtime attend deux secondes, puis vérifie s’il existe un autre message.
- Lorsqu’aucun message n’est trouvé, il attend environ quatre secondes avant de réessayer.
- Après plusieurs échecs de tentatives d’obtention d’un message de file d’attente, le temps d’attente continue à augmenter jusqu’à ce qu’il atteigne le délai d’attente maximal par défaut (une minute).
- La durée d’attente maximale est configurable via la propriété `maxPollingInterval` dans le [fichier host.json](functions-host-json.md#queues).

Pour le développement local, l’intervalle d’interrogation maximal par défaut est de deux secondes.

En ce qui concerne la facturation, le temps passé par le runtime à interroger est « gratuit » et n’est pas compté sur votre compte.

## <a name="trigger---concurrency"></a>Déclencheur : concurrence

Lorsque plusieurs messages de file d’attente attendent, le déclencheur de file d’attente récupère un lot de messages et appelle les instances de fonction simultanément pour les traiter. Par défaut, la taille de lot est de 16. Quand le nombre de messages en cours de traitement descend à 8, le runtime obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages traités simultanément par fonction sur une machine virtuelle est de 24. Cette limite s’applique séparément à chaque fonction déclenchée par une file d’attente sur chaque machine virtuelle. Si votre application de fonction est étendue à plusieurs machines virtuelles, chaque machine virtuelle attend des déclencheurs et essaie d’exécuter les fonctions. Par exemple, si une application de fonction est étendue à 3 machines virtuelles, le nombre maximal d’instances simultanées d’une fonction déclenché par une file d’attente est par défaut de 72.

La taille du lot et le seuil d’obtention d’un nouveau lot sont configurables dans le [fichier host.json](functions-host-json.md#queues). Si vous souhaitez minimiser l’exécution en parallèle des fonctions déclenchées par une file d’attente dans une application de fonction, vous pouvez définir la taille du lot sur 1. Ce paramètre évite les opérations simultanées uniquement pendant l’exécution de votre application de fonction sur une machine virtuelle unique. 

Le déclencheur de la file d’attente empêche automatiquement une fonction de traiter un message de file d’attente plusieurs fois ; les fonctions ne doivent pas nécessairement être écrites pour être idempotent.

## <a name="trigger---hostjson-properties"></a>Déclencheur - propriétés de host.json

Le fichier [host.json](functions-host-json.md#queues) contient les paramètres qui contrôlent le comportement du déclencheur de file d’attente. Consultez la section [Paramètres host.json](#hostjson-settings) pour plus d’informations concernant les paramètres disponibles.

## <a name="output"></a>Output

Utilisez la liaison de sortie Stockage File d’attente Azure pour écrire des messages dans une file d’attente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

L’exemple suivant montre un code [de fonction C#](functions-dotnet-class-library.md) qui crée un message de file d’attente pour chaque requête HTTP reçue.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de déclencheur HTTP dans un fichier *function.json*, ainsi que le code du [script C# (.csx)](functions-reference-csharp.md) qui utilise cette liaison. La fonction crée un élément de file d’attente avec une charge utile d’objet **CustomQueueMessage** pour chaque requête HTTP reçue.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code Script C# qui crée un message de file d’attente unique :

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Vous pouvez envoyer plusieurs messages à la fois en utilisant un paramètre `ICollector` ou `IAsyncCollector`. Voici le code Script C# qui envoie plusieurs messages, l’un avec les données de requête HTTP, et l’autre avec des valeurs codées en dur :

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de déclencheur HTTP dans un fichier *function.json*, et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction crée un élément de file d’attente pour chaque requête HTTP reçue.

Voici le fichier *function.json* :

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

La section [configuration](#output---configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Vous pouvez envoyer plusieurs messages à la fois en définissant un tableau de messages pour la liaison de sortie `myQueueItem`. Le code JavaScript suivant envoie deux messages de file d’attente avec des valeurs codées en dur pour chaque requête HTTP reçue.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre comment sortir des valeurs uniques et multiples dans les files d’attente de stockage. La configuration requise pour *function.json* est la même dans les deux sens.

Une liaison de file d’attente de stockage est définie dans *function.json*, où *Type* est défini sur `queue`.

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Pour définir un message individuel sur la file d’attente, vous passez une valeur unique à la méthode `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Pour créer plusieurs messages dans la file d’attente, déclarez un paramètre comme type de liste approprié et transmettez un tableau de valeurs (correspondant au type de liste) à la méthode `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

 L’exemple ci-après montre une fonction Java qui crée un message de file d’attente en cas de déclenchement par une requête HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Dans la [bibliothèque runtime des fonctions Java](/java/api/overview/azure/functions/runtime), utilisez l’annotation `@QueueOutput` sur les paramètres dont la valeur serait écrite dans Stockage File d’attente.  Le type de paramètre doit être `OutputBinding<T>`, où `T` est n’importe quel type Java natif d’un POJO.

---

## <a name="output---attributes-and-annotations"></a>Sortie - Attributs et annotations

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

L’attribut s’applique à un paramètre `out` ou à la valeur de retour de la fonction. Le constructeur de l’attribut prend le nom de la file d’attente, comme illustré dans l’exemple suivant :

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Vous pouvez définir la propriété `Connection` pour spécifier le compte de stockage à utiliser, comme indiqué dans l’exemple suivant :

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Pour obtenir un exemple complet, consultez [Sortie - exemple C#](#output).

Vous pouvez utiliser l’attribut `StorageAccount` pour spécifier le compte de stockage au niveau de la classe, de la méthode ou du paramètre. Pour plus d’informations, consultez Déclencheur - attributs.

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="javatabjava"></a>[Java](#tab/java)

L’annotation `QueueOutput` vous donne l’accès nécessaire pour écrire un message de sortie d’une fonction. L’exemple suivant montre une fonction déclenchée par HTTP qui crée un message de file d’attente.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Propriété    | Description |
|-------------|-----------------------------|
|`name`       | Déclare le nom du paramètre dans la signature de la fonction. Quand la fonction est déclenchée, la valeur de ce paramètre a le contenu du message de la file d’attente. |
|`queueName`  | Déclare le nom de la file d’attente dans le compte de stockage. |
|`connection` | Pointe vers la chaîne de connexion du compte de stockage. |

Le paramètre associé à l’annotation `QueueOutput` est typée en tant qu’instance [OutputBinding\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java).

---

## <a name="output---configuration"></a>Sortie - configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `Queue`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Cette propriété doit être définie sur `queue`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure.|
|**direction** | n/a | Cette propriété doit être définie sur `out`. Cette propriété est définie automatiquement lorsque vous créez le déclencheur dans le portail Azure. |
|**name** | n/a | Nom de la variable qui représente la file d’attente dans le code de la fonction. La valeur doit être `$return` pour faire référence à la valeur de retour de la fonction.|
|**queueName** |**QueueName** | Nom de la file d'attente. |
|**connection** | **Connection** |Nom d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser pour cette liaison. Si le nom du paramètre d’application commence par « AzureWebJobs », vous ne pouvez spécifier que le reste du nom ici. Par exemple, si vous définissez `connection` sur « MyStorage », le runtime Functions recherche un paramètre d’application qui est nommé « MyStorage ». Si vous laissez `connection` vide, le runtime Functions utilise la chaîne de connexion de stockage par défaut dans le paramètre d’application nommé `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Sortie - utilisation

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Écrivez un message de file d’attente unique en utilisant un paramètre de méthode comme `out T paramName`. Vous pouvez utiliser le type de retour de la méthode au lieu d’un paramètre `out`, et `T` peut être un des types suivants :

* Un objet sérialisable au format JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Si vous essayez de lier à `CloudQueueMessage` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

En C# et Script C#, écrivez plusieurs messages de file d’attente à l’aide d’un des types suivants : 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Écrivez un message de file d’attente unique en utilisant un paramètre de méthode comme `out T paramName`. `paramName` est la valeur spécifiée dans la propriété `name` de *function.json*. Vous pouvez utiliser le type de retour de la méthode au lieu d’un paramètre `out`, et `T` peut être un des types suivants :

* Un objet sérialisable au format JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Si vous essayez de lier à `CloudQueueMessage` et obtenez un message d’erreur, vérifiez que vous avez une référence à [la bonne version du SDK Stockage](#azure-storage-sdk-version-in-functions-1x).

En C# et Script C#, écrivez plusieurs messages de file d’attente à l’aide d’un des types suivants : 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

L’élément de la file d’attente en sortie est disponible via `context.bindings.<NAME>`, où `<NAME>` correspond au nom défini dans *function.json*. Vous pouvez utiliser une chaîne ou un objet sérialisable JSON pour la charge utile de l’élément de file d’attente.

# <a name="pythontabpython"></a>[Python](#tab/python)

Il existe deux options pour produire en sortie un message Event Hub à partir d’une fonction :

- **Valeur de retour** : Définissez la propriété `name` dans *function.json* sur `$return`. Avec cette configuration, la valeur de retour de la fonction est conservée sous la forme d’un message de Stockage File d’attente.

- **Impératif** : Passez une valeur à la méthode [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) du paramètre déclaré en tant que type [Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python). La valeur passée à `set` est conservée en tant que message de Stockage File d’attente.

# <a name="javatabjava"></a>[Java](#tab/java)

Il existe deux options pour produire en sortie un message Event hub à partir d’une fonction en utilisant l’annotation [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Valeur de retour** : En appliquant l’annotation à la fonction elle-même, la valeur de retour de la fonction est conservée sous la forme d’un message Event Hub.

- **Impératif** : Pour définir explicitement la valeur du message, appliquez l’annotation à un paramètre spécifique du type [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), où `T` est un POJO ou n’importe quel type Java natif. Avec cette configuration, le passage d’une valeur à la méthode `setValue` rend la valeur persistante en tant que message Event Hub.

---

## <a name="exceptions-and-return-codes"></a>Exceptions et codes de retour

| Liaison |  Informations de référence |
|---|---|
| File d'attente | [Codes d’erreur de file d’attente](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Objet blob, Table, File d’attente | [Codes d’erreur de stockage](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Objet blob, Table, File d’attente |  [Dépannage](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration globaux disponibles pour cette liaison dans les versions 2.x et ultérieures. L’exemple de fichier host.json ci-dessous contient seulement les paramètres des versions 2.x et ultérieures pour cette liaison. Pour plus d’informations sur les paramètres de configuration globaux dans les versions 2.x et ultérieures, consultez [Informations de référence sur le fichier host.json pour Azure Functions](functions-host-json.md).

> [!NOTE]
> Pour obtenir une référence de host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Intervalle maximal entre les interrogations de la file d’attente. L’intervalle minimum est de 00:00:00.100 (100 ms) et il est augmenté par incréments de 00:01:00 (1 minute).  Dans 1.x, le type de données est « milliseconds » (millisecondes) et, dans 2.x et ultérieur, il s’agit de « TimeSpan » (intervalle de temps).|
|visibilityTimeout|00:00:00|Intervalle de temps entre les nouvelles tentatives en cas d’échec du traitement d’un message. |
|batchSize|16|Le nombre de messages de file d’attente que le runtime Functions récupère simultanément et traite en parallèle. Quand le nombre de messages en cours de traitement descend à `newBatchThreshold`, le runtime obtient un autre lot et commence à traiter ces messages. Par conséquent, le nombre maximal de messages traités simultanément par fonction est `batchSize` plus `newBatchThreshold`. Cette limite s’applique séparément à chaque fonction déclenchée par une file d’attente. <br><br>Si vous souhaitez éviter les exécutions parallèles pour les messages reçus sur une file d’attente, vous pouvez définir `batchSize` sur 1. Toutefois, ce paramètre évite les opérations simultanées uniquement pendant l’exécution de votre application de fonction sur une machine virtuelle unique. Si l’application de fonction augmente la taille des instances sur plusieurs machines virtuelles, chaque machine virtuelle peut exécuter une instance de chaque fonction déclenchée par une file d’attente.<br><br>La valeur `batchSize` maximale est de 32. |
|maxDequeueCount|5|Nombre de tentatives de traitement d’un message avant de le placer dans la file d’attente de messages incohérents.|
|newBatchThreshold|batchSize/2|Quand le nombre de messages traités simultanément passe en dessous de cette valeur, le runtime récupère un autre lot.|

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Accéder à un didacticiel qui utilise une liaison de sortie de stockage de file d’attente](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
