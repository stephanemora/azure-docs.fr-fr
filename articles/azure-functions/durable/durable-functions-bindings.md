---
title: Liaisons pour Fonctions durables - Azure
description: Guide pratique pour utiliser des déclencheurs et des liaisons pour l’extension Durable Functions pour Azure Functions.
ms.topic: conceptual
ms.date: 05/07/2021
ms.author: azfuncdf
ms.openlocfilehash: a07748f996788825b21b5c23a117954085dadcbf
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656935"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Liaisons pour Fonctions durables (Azure Functions)

L'extension [Durable Functions](durable-functions-overview.md) introduit trois liaisons de déclencheur qui contrôlent l'exécution des fonctions d'orchestrateur, d'entité et d'activité. Elle introduit également une liaison de sortie qui agit comme un client pour l’exécution de Fonctions durables.

## <a name="orchestration-trigger"></a>Déclencheur d'orchestration

Le déclencheur d’orchestration vous permet de créer des [fonctions d’orchestrateur durables](durable-functions-types-features-overview.md#orchestrator-functions). Ce déclencheur s'exécute lorsqu'une nouvelle instance d'orchestration est planifiée et lorsqu'une instance d'orchestration existante reçoit un événement. Parmi les exemples d'événements susceptibles de déclencher les fonctions d'orchestrateur, citons les expirations du minuteur durable, les réponses aux fonctions d'activité et les événements déclenchés par des clients externes.

Lorsque vous créez des fonctions dans .NET, le déclencheur d'orchestration est configuré à l'aide de l'attribut .NET [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute).

Lorsque vous écrivez des fonctions d'orchestrateur dans des langages de script, comme JavaScript, Python ou PowerShell, le déclencheur d'orchestration est défini par l'objet JSON suivant dans le tableau `bindings` du fichier *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` correspond au nom de l’orchestration que les clients doivent utiliser lorsqu’ils souhaitent démarrer de nouvelles instances de cette fonction d’orchestrateur. Cette propriété est facultative. Si cette valeur n’est pas spécifiée, le nom de la fonction est utilisé.

En interne, cette liaison de déclencheur interroge le magasin durable configuré à la recherche de nouveaux événements d'orchestration, tels que des événements de démarrage d'orchestration, des événements d'expiration du minuteur durable, des événements de réponse aux fonctions d'activité et des événements externes déclenchés par d'autres fonctions.

### <a name="trigger-behavior"></a>Comportement du déclencheur

Voici quelques remarques concernant le déclencheur d’orchestration :

* **Thread unique** : un thread de répartiteur unique est utilisé pour toutes les exécutions d’une fonction d’orchestrateur sur une même instance d’hôte. C'est pourquoi il est important de s'assurer que le code de la fonction d'orchestrateur est efficace et n'effectue aucune opération E/S. Il est également important de s’assurer que ce thread n’effectue aucun travail asynchrone, sauf lorsqu’il attend des types de tâches Fonctions durables spécifiques.
* **Gestion des messages incohérents** : les déclencheurs d'orchestration ne prennent en charge aucun message incohérent.
* **Visibilité des messages** : les messages du déclencheur d’orchestration sont supprimés de la file d’attente et restent invisibles pour une durée configurable. La visibilité de ces messages est renouvelée automatiquement tant que l’application de la fonction est en cours d’exécution et saine.
* **Valeurs de retour** : les valeurs de retour sont sérialisées au format JSON et conservées dans la table d’historique d’orchestration du stockage Azure Table. Ces valeurs de retour peuvent être interrogées par la liaison du client d’orchestration, décrite plus loin.

> [!WARNING]
> Les fonctions d’orchestrateur ne doivent jamais utiliser une liaison d’entrée ou de sortie autre que la liaison du déclencheur d’orchestration. Cela pourrait entraîner des problèmes avec l’extension Tâche durable car ces liaisons risquent de ne pas respecter les règles de thread unique et d’E/S. Si vous souhaitez utiliser d'autres liaisons, ajoutez-les à une fonction d'activité appelée à partir de votre fonction d'orchestrateur. Pour plus d'informations sur les contraintes de codage des fonctions d'orchestrateur, consultez la documentation [Contraintes liées au code des fonctions d'orchestrateur](durable-functions-code-constraints.md).

> [!WARNING]
> Les fonctions d'orchestrateur JavaScript et Python ne doivent jamais être déclarées avec la propriété `async`.

### <a name="trigger-usage"></a>Utilisation du déclencheur

La liaison du déclencheur d’orchestration prend en charge à la fois les entrées et les sorties. Voici quelques éléments à connaître concernant la gestion des entrées et des sorties :

* **entrées** : les déclencheurs d'orchestration peuvent être appelés avec des entrées, accessibles via l'objet d'entrée de contexte. Toutes les entrées doivent être de type JSON sérialisable.
* **sorties** : les déclencheurs d’orchestration prennent en charge les valeurs de sortie ainsi que les entrées. La valeur de retour de la fonction sert à affecter la valeur de sortie et doit être de type JSON sérialisable.

### <a name="trigger-sample"></a>Exemple de déclencheur

L'exemple de code suivant illustre une simple fonction d’orchestrateur « Hello World » :

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    // ... do some work ...
    return $"Hello {name}!";
}
```

> [!NOTE]
> Le code précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    // ... do some work ...
    return `Hello ${name}!`;
});
```

> [!NOTE]
> La bibliothèque `durable-functions` prend en charge l'appel de la méthode `context.done` lorsque la fonction de générateur se termine.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    # Do some work
    return f"Hello {name}!"

main = df.Orchestrator.create(orchestrator_function)
```

---

La plupart des fonctions d’orchestrateur appellent des fonctions d’activité. Voici un exemple « Hello World » qui montre comment appeler une fonction d’activité :

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> Le code précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser `DurableOrchestrationContext` au lieu de `IDurableOrchestrationContext`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    input_ = context.get_input()
    result = yield context.call_activity('SayHello', name)
    return result

main = df.Orchestrator.create(orchestrator_function)
```

---

## <a name="activity-trigger"></a>Déclencheur d'activité

Le déclencheur d’activité vous permet de créer des fonctions appelées par des fonctions d’orchestrateur, appelées [fonctions d'activité](durable-functions-types-features-overview.md#activity-functions).

Si vous créez des fonctions dans .NET, le déclencheur d'activité est configuré à l'aide de l'attribut .NET [ActvityTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.activitytriggerattribute).

Si vous utilisez JavaScript, Python ou PowerShell, le déclencheur d'activité est défini par l'objet JSON suivant dans le tableau `bindings` du fichier *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` est le nom de l’activité. Cette valeur correspond au nom qu’utilisent les fonctions d’orchestrateur pour appeler cette fonction d’activité. Cette propriété est facultative. Si cette valeur n’est pas spécifiée, le nom de la fonction est utilisé.

En interne, cette liaison de déclencheur interroge le magasin durable configuré à la recherche de nouveaux événements d'exécution d'activité.

### <a name="trigger-behavior"></a>Comportement du déclencheur

Voici quelques remarques concernant le déclencheur d’activité :

* **Threading** : contrairement au déclencheur d’orchestration, les déclencheurs d’activité ne sont soumis à aucune restriction au niveau du thread ou des opérations d’E/S. Ils peuvent être traités comme des fonctions normales.
* **Gestion des messages incohérents** : les déclencheurs d'activité ne prennent en charge aucun message incohérent.
* **Visibilité des messages** : les messages du déclencheur d’activité sont supprimés de la file d’attente et restent invisibles pour une durée configurable. La visibilité de ces messages est renouvelée automatiquement tant que l’application de la fonction est en cours d’exécution et saine.
* **Valeurs de retour** : les valeurs de retour sont sérialisées au format JSON et conservées dans le magasin durable configuré.

### <a name="trigger-usage"></a>Utilisation du déclencheur

La liaison du déclencheur d’activité prend en charge à la fois les entrées et les sorties, exactement comme le déclencheur d’orchestration. Voici quelques éléments à connaître concernant la gestion des entrées et des sorties :

* **entrées** : les déclencheurs d'activité peuvent être appelés avec des entrées provenant d'une fonction d'orchestrateur. Toutes les entrées doivent être de type JSON sérialisable.
* **sorties** : les fonctions d’activité prennent en charge les valeurs de sortie ainsi que les entrées. La valeur de retour de la fonction sert à affecter la valeur de sortie et doit être de type JSON sérialisable.
* **métadonnées** : les fonctions d'activité .NET peuvent être liées à un paramètre `string instanceId` pour obtenir l'ID d'instance de l'orchestration d'appel.

### <a name="trigger-sample"></a>Exemple de déclencheur

L'exemple de code suivant illustre une simple fonction d'activité `SayHello` :

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Le type de paramètre par défaut pour la liaison .NET `ActivityTriggerAttribute` est [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableactivitycontext) (ou [DurableActivityContext](/dotnet/api/microsoft.azure.webjobs.durableactivitycontext?view=azure-dotnet-legacy&preserve-view=true) pour Durable Functions v1). Cependant, les déclencheurs d’activité .NET prennent également en charge la liaison directe avec des types sérialisables au format JSON (notamment les types primitifs). La même fonction peut donc être simplifiée comme suit :

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Les liaisons JavaScript peuvent également être transmises en tant que paramètres supplémentaires. La même fonction peut donc être simplifiée comme suit :

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

# <a name="python"></a>[Python](#tab/python)

```python
def main(name: str) -> str:
    return f"Hello {name}!"
```

---

### <a name="using-input-and-output-bindings"></a>Utilisation des liaisons d’entrée et de sortie

Vous pouvez utiliser des liaisons d’entrée et de sortie standard en plus de la liaison de déclencheur d’activité. Par exemple, vous pouvez prendre l’entrée de votre liaison d’activité et envoyer un message à un EventHub à l’aide de la liaison de sortie EventHub :

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Client d’orchestration

La liaison du client d’orchestration vous permet d’écrire des fonctions qui interagissent avec les fonctions d’orchestrateur. Ces fonctions sont souvent appelées [fonctions clientes](durable-functions-types-features-overview.md#client-functions). Par exemple, vous pouvez agir sur les instances de l’orchestration de l’une des manières suivantes :

* Les démarrer.
* Interroger leur état.
* Les arrêter.
* Leur envoyer des événements pendant qu’elles sont exécutées.
* Purger l’historique d’instance.

Si vous utilisez .NET, vous pouvez établir une liaison avec le client d'orchestration à l'aide de l'attribut [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute) ([OrchestrationClientAttribute](/dotnet/api/microsoft.azure.webjobs.orchestrationclientattribute?view=azure-dotnet-legacy&preserve-view=true) dans Durable Functions v1.x).

Si vous utilisez des langages de script, comme JavaScript, Python ou PowerShell, le déclencheur de client durable est défini par l'objet JSON suivant dans le tableau `bindings` du fichier *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` : utilisé dans les scénarios où plusieurs applications de fonction partagent le même compte de stockage, mais doivent être isolées les unes des autres. Si ce champ n’est pas spécifié, la valeur `host.json` sera utilisée par défaut. Cette valeur doit correspondre à la valeur utilisée par les fonctions d’orchestrateur cible.
* `connectionName` : nom d’un paramètre d’application contenant une chaîne de connexion du compte de stockage. Le compte de stockage représenté par cette chaîne de connexion doit être le même que celui utilisé par les fonctions d’orchestrateur cible. Si elle n’est pas spécifiée, la chaîne de connexion du compte de stockage par défaut pour l’application de la fonction est utilisée.

> [!NOTE]
> Dans la plupart des cas, nous vous recommandons d’omettre ces propriétés et de vous appuyer sur le comportement par défaut.

### <a name="client-usage"></a>Utilisation du client

Dans les fonctions .NET, vous établissez généralement une liaison avec [IDurableClient](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableclient) ([DurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.durableorchestrationclient?view=azure-dotnet-legacy&preserve-view=true) dans Durable Functions v1.x), ce qui vous donne un accès total à toutes les API clientes d'orchestration prises en charge par Durable Functions. Dans d'autres langages, vous devez utiliser le kit de développement logiciel (SDK) spécifique au langage pour accéder à un objet client.

Voici un exemple de fonction déclenchée par une file d'attente qui démarre une orchestration « HelloWorld ».

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> Le code C# précédent correspond à Durable Functions 2.x. Pour Durable Functions 1.x, vous devez utiliser l’attribut `OrchestrationClient` au lieu de l’attribut `DurableClient`, et vous devez utiliser le type de paramètre `DurableOrchestrationClient` au lieu de `IDurableOrchestrationClient`. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**
```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

**__init__.py**
```python
import json
import azure.functions as func
import azure.durable_functions as df

async def main(msg: func.QueueMessage, starter: str) -> None:
    client = df.DurableOrchestrationClient(starter)
    payload = msg.get_body().decode('utf-8')
    instance_id = await client.start_new("HelloWorld", client_input=payload)
```

---

Vous trouverez plus d’informations sur le démarrage des instances dans la section [Gestion d’instance](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Déclencheur d’entité

Les déclencheurs d’entité vous permettent de créer des [fonctions d'entité](durable-functions-entities.md). Ce déclencheur prend en charge le traitement des événements pour une instance d’entité spécifique.

> [!NOTE]
> Les déclencheurs d’entité sont disponibles à partir de la version Durable Functions 2.x.

En interne, cette liaison de déclencheur interroge le magasin durable configuré à la recherche de nouvelles opérations d'entité à exécuter.

### <a name="trigger-behavior"></a>Comportement du déclencheur

Voici quelques remarques concernant le déclencheur d’entité :

* **Thread unique** : un thread de répartiteur unique est utilisé pour traiter les opérations d’une entité donnée. Si plusieurs messages sont envoyés simultanément à une même entité, les opérations sont traitées tour à tour.
* **Gestion des messages incohérents** : les déclencheurs d'entité ne prennent en charge aucun message incohérent.
* **Visibilité des messages** : les messages du déclencheur d’entité sont supprimés de la file d’attente et restent invisibles pour une durée configurable. La visibilité de ces messages est renouvelée automatiquement tant que l’application de la fonction est en cours d’exécution et saine.
* **Valeurs de retour** : les fonctions d'entité ne prennent pas en charge les valeurs de retour. Des API spécifiques peuvent être utilisées pour enregistrer l’état ou transmettre les valeurs aux orchestrations.

Toute modification d’état apportée à une entité lors de son exécution perdure automatiquement au terme de l'exécution.

Pour plus d'informations et d'exemples sur la définition et l'interaction avec les déclencheurs d'entité, consultez la documentation relative aux [entités durables](durable-functions-entities.md).

## <a name="entity-client"></a>Client d'entité

La liaison du client d’entité vous permet de déclencher des [fonctions d'entité](#entity-trigger) de manière asynchrone. Ces fonctions sont parfois appelées [fonctions clientes](durable-functions-types-features-overview.md#client-functions).

Si vous utilisez des fonctions précompilées .NET, vous pouvez établir une liaison avec le client d'entité en utilisant l'attribut .NET [DurableClientAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durableclientattribute).

> [!NOTE]
> `[DurableClientAttribute]` permet de créer une liaison avec le [client d'orchestration](#orchestration-client).

Si vous utilisez des langages de script (comme C#, JavaScript ou Python) pour le développement, le déclencheur d'entité est défini par l'objet JSON suivant dans le tableau `bindings` du fichier *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` : utilisé dans les scénarios où plusieurs applications de fonction partagent le même compte de stockage, mais doivent être isolées les unes des autres. Si ce champ n’est pas spécifié, la valeur `host.json` sera utilisée par défaut. Cette valeur doit correspondre à la valeur utilisée par les fonctions d’entité cible.
* `connectionName` : nom d’un paramètre d’application contenant une chaîne de connexion du compte de stockage. Le compte de stockage représenté par cette chaîne de connexion doit être le même que celui utilisé par les fonctions d’entité cible. Si elle n’est pas spécifiée, la chaîne de connexion du compte de stockage par défaut pour l’application de la fonction est utilisée.

> [!NOTE]
> Dans la plupart des cas, nous vous recommandons d’omettre les propriétés facultatives et de vous appuyer sur le comportement par défaut.

Pour plus d'informations et d'exemples sur l'interaction avec les entités en tant que client, consultez la documentation relative aux [Entités durables](durable-functions-entities.md#access-entities).

<a name="host-json"></a>
## <a name="hostjson-settings"></a>Paramètres host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence sur l'API HTTP intégrée pour la gestion des instances](durable-functions-http-api.md)
