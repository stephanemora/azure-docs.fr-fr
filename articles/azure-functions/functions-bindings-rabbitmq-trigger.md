---
title: Déclencheur RabbitMQ pour Azure Functions
description: Apprenez à exécuter une fonction Azure quand un message RabbitMQ est créé.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 4ba19fdf700790d89fe04867985fb803c3b0a2fc
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760399"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Présentation du déclencheur RabbitMQ pour Azure Functions

> [!NOTE]
> Les liaisons RabbitMQ sont uniquement prises en charge de manière complète dans les plans **Premium et Dedicated**. La consommation n’est pas prise en charge.

Utilisez le déclencheur RabbitMQ pour répondre aux messages d’une file d’attente RabbitMQ.

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](functions-bindings-rabbitmq.md).

## <a name="example"></a>Exemple

# <a name="c"></a>[C#](#tab/csharp)

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui lit et journalise le message RabbitMQ en tant qu’[événement RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html) :

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

L’exemple suivant montre comment lire le message sous la forme d’un objet CLR traditionnel.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

À l’instar des objets JSON, une erreur se produit si le message n’est pas correctement mis en forme en tant qu’objet C#. Si c’est le cas, il est ensuite lié à la variable pocObj, qui peut être utilisée lorsque cela est nécessaire.

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison de déclencheur RabbitMQ dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction lit et journalise le message RabbitMQ.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Voici le code Script C# :

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

L’exemple suivant montre une liaison de déclencheur RabbitMQ dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison. La fonction lit et journalise un message RabbitMQ.

Voici les données de liaison dans le fichier *function.json* :

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Voici le code de script JavaScript :

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

L’exemple suivant montre comment lire un message de file d’attente RabbitMQ à l’aide d’un déclencheur.

Une liaison RabbitMQ est définie dans *function.json*, où le *type* est défini sur `RabbitMQTrigger`.

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

La fonction Java suivante utilise l’annotation `@RabbitMQTrigger` des [types RabbitMQ Java](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) pour décrire la configuration d’un déclencheur de file d’attente RabbitMQ. La fonction récupère le message placé dans la file d’attente et l’ajoute dans les journaux.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Attributs et annotations

# <a name="c"></a>[C#](#tab/csharp)

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs).

Voici un attribut `RabbitMQTrigger` dans une signature de méthode :

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

Vous trouverez un exemple complet sur la page [Exemple C#](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Les attributs ne sont pas pris en charge par le script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Les attributs ne sont pas pris en charge par JavaScript.

# <a name="python"></a>[Python](#tab/python)

Les attributs ne sont pas pris en charge par Python.

# <a name="java"></a>[Java](#tab/java)

L’annotation `RabbitMQTrigger` vous permet de créer une fonction qui s’exécute lors de la création d’un message RabbitMQ. Les options de configuration disponibles sont le nom de la file d’attente et le nom de la chaîne de connexion. Pour plus d’informations sur les paramètres, consultez les [annotations Java RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Pour plus de détails, voir l’[exemple](#example) de déclencheur.

---

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `RabbitMQTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type** | n/a | Doit être défini sur « RabbitMQTrigger ».|
|**direction** | n/a | Doit être défini sur « in ».|
|**name** | n/a | Nom de la variable qui représente la file d’attente dans le code de la fonction. |
|**queueName**|**QueueName**| Nom de la file d’attente à partir de laquelle les messages sont envoyés. |
|**hostName**|**HostName**|(ignorée si vous utilisez ConnectStringSetting) <br>Nom d’hôte de la file d’attente (par exemple : 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ignorée si vous utilisez ConnectionStringSetting) <br>Nom du paramètre d’application qui contient le nom d’utilisateur permettant d’accéder à la file d’attente. Exemple : UserNameSetting: "%< UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ignorée si vous utilisez ConnectionStringSetting) <br>Nom du paramètre d’application qui contient le mot de passe permettant d’accéder à la file d’attente. Exemple : PasswordSetting: "%< PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|Nom du paramètre d’application qui contient la chaîne de connexion de la file d’attente de messages RabbitMQ. Notez que si vous spécifiez la chaîne de connexion directement et non par le biais d’un paramètre d’application dans local.settings.json, le déclencheur ne fonctionnera pas. (exemple : dans *function.json* : connectionStringSetting: "rabbitMQConnection" <br> Dans *local.settings.json* : "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**Port**|(ignorée si vous utilisez ConnectionStringSetting) Obtient ou définit le port utilisé. La valeur par défaut est 0. Elle pointe vers le paramètre de port par défaut du client rabbitmq : 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Usage

# <a name="c"></a>[C#](#tab/csharp)

Le type de message par défaut est [Événement RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html). La propriété `Body` de l’événement RabbitMQ peut correspondre à l’un des types listés ci-dessous :

* `An object serializable as JSON` : le message est remis sous la forme d’une chaîne JSON valide.
* `string`
* `byte[]`
* `POCO` : le message est mis en forme en tant qu’objet C#. Vous trouverez un exemple complet sur la page [Exemple C#](#example).

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Le type de message par défaut est [Événement RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html). La propriété `Body` de l’événement RabbitMQ peut correspondre à l’un des types listés ci-dessous :

* `An object serializable as JSON` : le message est remis sous la forme d’une chaîne JSON valide.
* `string`
* `byte[]`
* `POCO` : le message est mis en forme en tant qu’objet C#. Pour obtenir un exemple complet, consultez l’[exemple](#example) de script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Le message en file d’attente est disponible via context.bindings.<NAME> où <NAME> correspond au nom défini dans function.json. Si la charge utile est JSON, la valeur est désérialisée en objet.

# <a name="python"></a>[Python](#tab/python)

Consultez l’[exemple](#example) en Python.

# <a name="java"></a>[Java](#tab/java)

Reportez-vous à la rubrique [Attributs et annotations Java](#attributes-and-annotations).

---

## <a name="dead-letter-queues"></a>Files d’attente de lettres mortes
Les files d’attente de lettres mortes et les échanges ne peuvent pas être contrôlés ou configurés à partir du déclencheur RabbitMQ.  Pour utiliser les files d’attente de lettres mortes, préconfigurez la file d’attente utilisée par le déclencheur dans RabbitMQ. Reportez-vous à la documentation de [RabbitMQ](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration globaux disponibles pour cette liaison dans les versions 2.x et ultérieures. L’exemple de fichier *host.json* ci-dessous contient seulement les paramètres pour cette liaison. Pour plus d’informations sur les paramètres de configuration globale, consultez [Informations de référence sur le fichier host.json pour la version d’Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|prefetchCount|30|Obtient ou définit le nombre de messages que le destinataire des messages peut demander simultanément et est mis en cache.|
|queueName|n/a| Nom de la file d’attente à partir de laquelle les messages sont envoyés.|
|connectionString|n/a|Chaîne de connexion de la file d’attente de messages RabbitMQ. Notez que la chaîne de connexion est directement spécifiée ici et non via un paramètre d’application.|
|port|0|(ignorée si vous utilisez connectionString) Obtient ou définit le port utilisé. La valeur par défaut est 0. Elle pointe vers le paramètre de port par défaut du client rabbitmq : 5672.|

## <a name="local-testing"></a>Test local

> [!NOTE]
> ConnectionString est prioritaire sur « hostName », « userName » et « password ». S’ils sont tous définis, connectionString remplace les deux autres.

Si vous effectuez des tests localement sans chaîne de connexion, vous devez définir le paramètre « hostName » ainsi que « userName » et « password », le cas échéant, dans la section « rabbitMQ » de *host.json* :

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Propriété  |Default | Description |
|---------|---------|---------|
|hostName|n/a|(ignorée si vous utilisez connectionString) <br>Nom d’hôte de la file d’attente (par exemple : 10.26.45.210)|
|userName|n/a|(ignorée si vous utilisez connectionString) <br>Nom pour accéder à la file d’attente |
|mot de passe|n/a|(ignorée si vous utilisez connectionString) <br>Mot de passe pour accéder à la file d’attente|


## <a name="enable-runtime-scaling"></a>Activer la mise à l’échelle du runtime

Pour permettre un scale-out du déclencheur RabbitMQ sur plusieurs instances, le paramètre de **supervision de la mise à l’échelle du runtime** doit être activé. 

Dans le portail, ce paramètre se trouve sous **Configuration** > **Paramètres d’exécution de la fonction** pour votre application de fonction.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

Dans l’interface CLI, vous pouvez activer la **supervision de la mise à l’échelle du runtime** à l’aide de la commande suivante :

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>Surveillance du point de terminaison RabbitMQ
Pour surveiller vos files d’attente et échanges pour un certain point de terminaison RabbitMQ :

* Activez le [plug-in de gestion RabbitMQ](https://www.rabbitmq.com/management.html).
* Accédez à http://{node-hostname}:15672 et connectez-vous avec votre nom d’utilisateur et votre mot de passe.

## <a name="next-steps"></a>Étapes suivantes

- [Envoyer des messages RabbitMQ à partir d’Azure Functions (liaison de sortie)](./functions-bindings-rabbitmq-output.md)
