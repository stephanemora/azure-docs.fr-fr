---
title: Liaisons pour Fonctions durables - Azure
description: Guide pratique pour utiliser des déclencheurs et des liaisons pour l’extension Durable Functions pour Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033582"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Liaisons pour Fonctions durables (Azure Functions)

L’extension [Fonctions durables](durable-functions-overview.md) introduit deux nouvelles liaisons de déclencheur qui contrôlent l’exécution des fonctions d’orchestrateur et d’activité. Elle introduit également une liaison de sortie qui agit comme un client pour l’exécution de Fonctions durables.

## <a name="orchestration-trigger"></a>Déclencheur d'orchestration

Le déclencheur d’orchestration vous permet de créer des [fonctions d’orchestrateur durables](durable-functions-types-features-overview.md#orchestrator-functions). Ce déclencheur prend en charge le démarrage de nouvelles instances de fonctions d’orchestrateur et la reprise d’instances de fonctions d’orchestrateur existantes « en attente » d’une tâche.

Lorsque vous utilisez les outils Visual Studio pour Azure Functions, le déclencheur d’orchestration est configuré à l’aide de l’attribut .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

Lorsque vous écrivez des fonctions d’orchestrateur dans des langages de script (par exemple, JavaScript ou C#), le déclencheur d’orchestration est défini par l’objet JSON suivant dans le tableau `bindings` du fichier *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` est le nom de l’orchestration. Il s’agit de la valeur que les clients doivent utiliser lorsqu’ils souhaitent démarrer de nouvelles instances de cette fonction d’orchestrateur. Cette propriété est facultative. Si cette valeur n’est pas spécifiée, le nom de la fonction est utilisé.

En interne, cette liaison de déclencheur interroge une série de files d’attente dans le compte de stockage par défaut pour l’application de la fonction. Ces files d’attente sont des détails d’implémentation internes de l’extension, et elles ne sont donc pas explicitement configurées dans les propriétés de liaison.

### <a name="trigger-behavior"></a>Comportement du déclencheur

Voici quelques remarques concernant le déclencheur d’orchestration :

* **Thread unique** : un thread de répartiteur unique est utilisé pour toutes les exécutions d’une fonction d’orchestrateur sur une même instance d’hôte. Pour cette raison, il est important de s’assurer que le code de la fonction d’orchestrateur est efficace et n’effectue aucune opération E/S. Il est également important de s’assurer que ce thread n’effectue aucun travail asynchrone, sauf lorsqu’il attend des types de tâches Fonctions durables spécifiques.
* **Gestion des messages incohérents** : les déclencheurs d’orchestration ne prennent en charge aucun message incohérent.
* **Visibilité des messages** : les messages du déclencheur d’orchestration sont supprimés de la file d’attente et restent invisibles pour une durée configurable. La visibilité de ces messages est renouvelée automatiquement tant que l’application de la fonction est en cours d’exécution et saine.
* **Valeurs de retour** : les valeurs de retour sont sérialisées au format JSON et conservées dans la table d’historique d’orchestration du stockage Azure Table. Ces valeurs de retour peuvent être interrogées par la liaison du client d’orchestration, décrite plus loin.

> [!WARNING]
> Les fonctions d’orchestrateur ne doivent jamais utiliser une liaison d’entrée ou de sortie autre que la liaison du déclencheur d’orchestration. Cela pourrait entraîner des problèmes avec l’extension Tâche durable car ces liaisons risquent de ne pas respecter les règles de thread unique et d’E/S. Si vous souhaitez utiliser d’autres liaisons, ajoutez-les à une fonction d’activité appelée à partir de votre fonction d’orchestrateur.

> [!WARNING]
> Les fonctions d’orchestrateur JavaScript ne doivent jamais être déclarées avec la propriété `async`.

### <a name="trigger-usage-net"></a>Utilisation du déclencheur (.NET)

La liaison du déclencheur d’orchestration prend en charge à la fois les entrées et les sorties. Voici quelques éléments à connaître concernant la gestion des entrées et des sorties :

* **entrées** : les fonctions d’orchestration .NET prennent uniquement en charge le type de paramètre [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Les entrées de désérialisation directement dans la signature de la fonction ne sont pas prises en charge. Le code doit utiliser la méthode [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.NET) ou `getInput` (JavaScript) pour extraire des entrées de la fonction d’orchestrateur. Ces entrées doivent être de type JSON sérialisable.
* **sorties** : les déclencheurs d’orchestration prennent en charge les valeurs de sortie ainsi que les entrées. La valeur de retour de la fonction sert à affecter la valeur de sortie et doit être de type JSON sérialisable. Si une fonction .NET retourne `Task` ou `void`, une valeur `null` est enregistrée en tant que sortie.

### <a name="trigger-sample"></a>Exemple de déclencheur

L'exemple de code suivant illustre une simple fonction d’orchestrateur « Hello World » :

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> L’objet `context` dans JavaScript représente non pas le contexte d’orchestration durable mais le contexte de fonction [ dans son ensemble](../functions-reference-node.md#context-object). Vous pouvez accéder à des méthodes d’orchestration via la propriété `df` de l’objet `context`.

> [!NOTE]
> Les orchestrateurs JavaScript doivent utiliser `return`. La bibliothèque `durable-functions` prend en charge l’appel de la méthode `context.done`.

La plupart des fonctions d’orchestrateur appellent des fonctions d’activité. Voici un exemple « Hello World » qui montre comment appeler une fonction d’activité :

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Déclencheur d'activité

Le déclencheur d’activité vous permet de créer des fonctions appelées par des fonctions d’orchestrateur, appelées [fonctions d'activité](durable-functions-types-features-overview.md#activity-functions).

Si vous utilisez Visual Studio, le déclencheur d’activité est configuré à l’aide de l’attribut .NET [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html).

Si vous utilisez VS Code ou le portail Azure pour le développement, le déclencheur d’activité est défini par l’objet JSON suivant dans le tableau `bindings` de *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` est le nom de l’activité. Cette valeur correspond au nom qu’utilisent les fonctions d’orchestrateur pour appeler cette fonction d’activité. Cette propriété est facultative. Si cette valeur n’est pas spécifiée, le nom de la fonction est utilisé.

En interne, cette liaison de déclencheur interroge une file d’attente dans le compte de stockage par défaut pour l’application de la fonction. Cette file d’attente est un détail d’implémentation interne de l’extension, et elle n’est donc pas explicitement configurée dans les propriétés de liaison.

### <a name="trigger-behavior"></a>Comportement du déclencheur

Voici quelques remarques concernant le déclencheur d’activité :

* **Threading** : contrairement au déclencheur d’orchestration, les déclencheurs d’activité ne sont soumis à aucune restriction au niveau du thread ou des opérations d’E/S. Ils peuvent être traités comme des fonctions normales.
* **Gestion des messages incohérents** : les déclencheurs d’activité ne prennent en charge aucun message incohérent.
* **Visibilité des messages** : les messages du déclencheur d’activité sont supprimés de la file d’attente et restent invisibles pour une durée configurable. La visibilité de ces messages est renouvelée automatiquement tant que l’application de la fonction est en cours d’exécution et saine.
* **Valeurs de retour** : les valeurs de retour sont sérialisées au format JSON et conservées dans la table d’historique d’orchestration du stockage Azure Table.

> [!WARNING]
> Le serveur principal de stockage pour les fonctions de l’activité est un détail d’implémentation, et le code utilisateur ne doit pas interagir directement avec ces entités de stockage.

### <a name="trigger-usage-net"></a>Utilisation du déclencheur (.NET)

La liaison du déclencheur d’activité prend en charge à la fois les entrées et les sorties, exactement comme le déclencheur d’orchestration. Voici quelques éléments à connaître concernant la gestion des entrées et des sorties :

* **entrées** : les fonctions d’activité .NET natives utilisent le type de paramètre [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html). Une fonction d’activité peut également être déclarée avec n’importe quel type de paramètre sérialisable au format JSON. Lorsque vous utilisez `DurableActivityContext`, vous pouvez appeler [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) pour extraire et désérialiser l’entrée de la fonction d’activité.
* **sorties** : les fonctions d’activité prennent en charge les valeurs de sortie ainsi que les entrées. La valeur de retour de la fonction sert à affecter la valeur de sortie et doit être de type JSON sérialisable. Si une fonction .NET retourne `Task` ou `void`, une valeur `null` est enregistrée en tant que sortie.
* **métadonnées** : les fonctions d’activité .NET peuvent être liées à un paramètre `string instanceId` pour obtenir l’ID d’instance de l’orchestration parente.

### <a name="trigger-sample"></a>Exemple de déclencheur

L'exemple de code suivant illustre une simple fonction d’activité « Hello World » :

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Le type de paramètre par défaut pour la liaison `ActivityTriggerAttribute` .NET est `DurableActivityContext`. Cependant, les déclencheurs d’activité .NET prennent également en charge la liaison directe avec des types sérialisables au format JSON (notamment les types primitifs). La même fonction peut donc être simplifiée comme suit :

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

La liaison du client d’orchestration vous permet d’écrire des fonctions qui interagissent avec les fonctions d’orchestrateur. Ces fonctions sont parfois appelées [fonctions clientes](durable-functions-types-features-overview.md#client-functions). Par exemple, vous pouvez agir sur les instances de l’orchestration de l’une des manières suivantes :

* Les démarrer.
* Interroger leur état.
* Les arrêter.
* Leur envoyer des événements pendant qu’elles sont exécutées.
* Purger l’historique d’instance.

Si vous utilisez Visual Studio, vous pouvez établir une liaison avec le client d’orchestration en utilisant l’attribut .NET [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) pour Durable Functions 1.0. À partir de la préversion Durable Functions 2.0, vous pouvez lier le client d’orchestration à l'aide de l'attribut `DurableClientAttribute` .NET.

Si vous utilisez des scripts de langage (par exemple, des fichiers *.csx* ou *.js*) pour le développement, le déclencheur d’orchestration est défini par l’objet JSON suivant dans le `bindings`tableau *du fichier function.json* :

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

Dans les fonctions .NET, vous établissez généralement une liaison avec `DurableOrchestrationClient`, ce qui vous donne un accès total à toutes les API clientes prises en charge par Durable Functions. À partir de Durable Functions 2.0, vous créez une liaison `IDurableOrchestrationClient` à l’interface. Dans JavaScript, les mêmes API sont exposées par l’objet renvoyé à partir de `getClient`. Les API sur l’objet client incluent :

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

Des liaisons .NET peuvent également être établies avec `IAsyncCollector<T>`, où `T` est [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) ou `JObject`.

Pour plus d'informations sur ces opérations, consultez la documentation de l’API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html).

### <a name="client-sample-visual-studio-development"></a>Exemple de client (développement Visual Studio)

Voici l’exemple d’une fonction déclenchée par une file d’attente qui démarre une orchestration « HelloWorld ».

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Exemple de client (sans Visual Studio)

Si vous n’utilisez pas Visual Studio pour le développement, vous pouvez créer le fichier *function.json* suivant. Cet exemple montre comment configurer une fonction déclenchée par une file d’attente, qui utilise la liaison du client d’orchestration durable :

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
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Voici des exemples spécifiques à un langage, qui démarrent de nouvelles instances de fonction d’orchestrateur.

#### <a name="c-sample"></a>Exemple de code C#

L’exemple suivant montre comment utiliser la liaison de client d’orchestration durable pour démarrer une nouvelle instance de fonction à partir d’une fonction de script C# :

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>Exemple JavaScript

L’exemple suivant montre comment utiliser la liaison de client d’orchestration durable pour démarrer une nouvelle instance de fonction à partir d’une fonction JavaScript :

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Vous trouverez plus d’informations sur le démarrage des instances dans la section [Gestion d’instance](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Déclencheur d’entité

Les déclencheurs d’entité vous permettent de créer des [fonctions d'entité](durable-functions-entities.md). Ce déclencheur prend en charge le traitement des événements pour une instance d’entité spécifique.

Lorsque vous utilisez les outils Visual Studio pour Azure Functions, le déclencheur d’entité est configuré à l’aide de l’attribut .NET `EntityTriggerAttribute`.

> [!NOTE]
> Les déclencheurs d’entité sont disponibles dans Durable Functions 2.0 et versions ultérieures. Les déclencheurs d’entité ne sont pas encore disponibles pour JavaScript.

En interne, cette liaison de déclencheur interroge une série de files d’attente dans le compte de stockage par défaut pour l’application de la fonction. Ces files d’attente sont des détails d’implémentation internes de l’extension, et elles ne sont donc pas explicitement configurées dans les propriétés de liaison.

### <a name="trigger-behavior"></a>Comportement du déclencheur

Voici quelques remarques concernant le déclencheur d’entité :

* **Thread unique** : un thread de répartiteur unique est utilisé pour traiter les opérations d’une entité donnée. Si plusieurs messages sont envoyés simultanément à une même entité, les opérations sont traitées tour à tour.
* **Gestion des messages incohérents** : les déclencheurs d’entité ne prennent en charge aucun message incohérent.
* **Visibilité des messages** : les messages du déclencheur d’entité sont supprimés de la file d’attente et restent invisibles pour une durée configurable. La visibilité de ces messages est renouvelée automatiquement tant que l’application de la fonction est en cours d’exécution et saine.
* **Valeurs de retour** : les fonctions d’entité ne prennent pas en charge les valeurs de retour. Des API spécifiques peuvent être utilisées pour enregistrer l’état ou transmettre les valeurs aux orchestrations.

Toute modification d’état apportée à une entité lors de son exécution perdure automatiquement au terme de l'exécution.

### <a name="trigger-usage-net"></a>Utilisation du déclencheur (.NET)

Chaque fonction d’entité dispose d'un type de paramètre de `IDurableEntityContext`, qui a les membres suivants :

* **EntityName** : Obtient le nom de l’entité en cours d’exécution.
* **EntityKey** : Obtient la clé de l’entité en cours d’exécution.
* **EntityId** : Obtient l’ID de l’entité en cours d’exécution.
* **OperationName** : Obtient le nom de l’opération en cours.
* **IsNewlyConstructed** : Renvoie `true` si l’entité n’existait pas avant l’opération.
* **GetState\<TState>()**  : Obtient l’état actuel de l’entité. Le paramètre `TState` doit correspondre à un type primitif ou sérialisable JSON.
* **SetState(object)**  : Met à jour l’état de l’entité. Le paramètre `object` doit correspondre à un objet primitif ou sérialisable JSON.
* **GetInput\<TInput()**  : Obtient l’entrée pour l’opération en cours. Le type de paramètre `TInput` doit représenter un type primitif ou sérialisable JSON.
* **Return(object)**  : Renvoie une valeur à l'orchestration qui a appelé l’opération. Le paramètre `object` doit correspondre à un objet primitif ou sérialisable JSON.
* **DestructOnExit()**  : Supprime l’entité après avoir terminé l’opération en cours.
* **SignalEntity(EntityId, string, object)**  : Envoie un message unidirectionnel à une entité. Le paramètre `object` doit correspondre à un objet primitif ou sérialisable JSON.

Lorsque vous utilisez le mode de programmation d’entité basé sur les classes, l'objet `IDurableEntityContext` peut être référencé à l'aide de la propriété à thread statique `Entity.Current`.

### <a name="trigger-sample---entity-function"></a>Exemple de déclencheur - fonction d'entité

Le code suivant est un exemple d’une entité de *Compteur* simple implémentée en tant que fonction standard. Cette fonction définit trois *opérations*, `add`, `reset` et `get`, chacune d’elles opérant sur une valeur d’état d’entier, `currentValue`.

```csharp
[FunctionName(nameof(Counter))]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

### <a name="trigger-sample---entity-class"></a>Exemple de déclencheur - classe d'entité

L’exemple suivant est une implémentation équivalente de l’entité `Counter` précédente à l’aide de classes et de méthodes .NET.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;
    
    public void Reset() => this.CurrentValue = 0;
    
    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

> [!NOTE]
> La méthode de point d’entrée de la fonction avec l’attribut `[FunctionName]` *doit* être déclarée `static` lors de l’utilisation de classes d’entité. Les méthodes de point d’entrée non statiques peuvent entraîner l’initialisation de plusieurs objets et éventuellement d’autres comportements non définis.

Les classes d’entité disposent des mécanismes spéciaux pour interagir avec les liaisons et l’injection de dépendance .NET. Pour plus d’informations, consultez l’article [Entités durables](durable-functions-entities.md).

## <a name="entity-client"></a>Client d'entité

La liaison du client d’entité vous permet de déclencher des [fonctions d'entité](#entity-trigger) de manière asynchrone. Ces fonctions sont parfois appelées [fonctions clientes](durable-functions-types-features-overview.md#client-functions).

Si vous utilisez Visual Studio, vous pouvez établir une liaison avec le client d’entité en utilisant l’attribut .NET `DurableClientAttribute`.

> [!NOTE]
> `[DurableClientAttribute]` permet de créer une liaison avec le [client d'orchestration](#orchestration-client).

Si vous utilisez des scripts de langage (par exemple, des fichiers *.csx* ou *.js*) pour le développement, le déclencheur d’entité est défini par l’objet JSON suivant dans le `bindings`tableau *du fichier function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub` : utilisé dans les scénarios où plusieurs applications de fonction partagent le même compte de stockage, mais doivent être isolées les unes des autres. Si ce champ n’est pas spécifié, la valeur `host.json` sera utilisée par défaut. Cette valeur doit correspondre à la valeur utilisée par les fonctions d’entité cible.
* `connectionName` : nom d’un paramètre d’application contenant une chaîne de connexion du compte de stockage. Le compte de stockage représenté par cette chaîne de connexion doit être le même que celui utilisé par les fonctions d’entité cible. Si elle n’est pas spécifiée, la chaîne de connexion du compte de stockage par défaut pour l’application de la fonction est utilisée.

> [!NOTE]
> Dans la plupart des cas, nous vous recommandons d’omettre les propriétés facultatives et de vous appuyer sur le comportement par défaut.

### <a name="entity-client-usage"></a>Utilisation du client d'entité

Dans les fonctions .NET, vous établissez généralement une liaison avec `IDurableEntityClient`, ce qui vous donne un accès total à toutes les API clientes prises en charge par les entités durables. Vous pouvez également créer une liaison avec l'interface`IDurableClient`, qui fournit l’accès aux API clientes pour les entités et les orchestrations. Les API sur l’objet client incluent :

* **ReadEntityStateAsync\<T>**  : Lit l’état d’une entité.
* **SignalEntityAsync** : Envoie un message unidirectionnel à une entité et attend qu’elle soit mise en file d’attente.
* **SignalEntityAsync\<TEntityInterface>**  : identique à `SignalEntityAsync`, mais utilise un objet proxy généré de type `TEntityInterface`.
* **CreateEntityProxy\<TEntityInterface >**  : génère dynamiquement un proxy dynamique de type `TEntityInterface` pour effectuer des appels de type sécurisé à destination des entités.

> [!NOTE]
> Il est important de comprendre que les opérations « signal » précédentes sont toutes asynchrones. Il n’est pas possible d’appeler une fonction d’entité et de récupérer une valeur de retour à partir d'un client. De même, `SignalEntityAsync` peut être renvoyé avant que l’entité ne commence à exécuter l’opération. Seules les fonctions d’orchestrateur peuvent appeler des fonctions d’entité de façon synchrone et traiter des valeurs de retour.

Les API `SignalEntityAsync` nécessitent de spécifier l'identificateur unique de l'entité en tant que `EntityId`. Ces API peuvent aussi prendre le nom de l’opération d’entité en tant que `string` et la charge utile de l’opération en tant que `object` sérialisable JSON. Si l’entité cible n’existe pas, elle est automatiquement créée avec l’ID d’entité spécifié.

### <a name="client-sample-untyped"></a>Exemple de client (non typé)

Voici un exemple de fonction déclenchée par une file d’attente qui appelle une entité « Compteur ».

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Exemple de client (typé)

Il est également possible de générer un objet proxy pour l’accès de type sécurisé aux opérations d'entité. Pour générer un proxy de type sécurisé, le type d’entité doit implémenter une interface. Par exemple, supposons que l’entité `Counter` mentionnée précédemment implémentait une interface `ICounter`, définie comme suit :

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Le code client peut ensuite utiliser `SignalEntityAsync<TEntityInterface>` et spécifier l’interface `ICounter` comme paramètre de type pour générer un proxy de type sécurisé. Cette utilisation de proxys de type sécurisé est illustrée dans l’exemple de code suivant :

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

Dans l’exemple précédent, le paramètre `proxy` est une instance générée dynamiquement de `ICounter`, qui traduit en interne l’appel à `Add` en un appel (non typé) équivalent à `SignalEntityAsync`.

Plusieurs règles permettent de définir des interfaces d’entité :

* Le paramètre de type `TEntityInterface` dans `SignalEntityAsync<TEntityInterface>`doit être une interface.
* Les interfaces d’entité doivent uniquement définir des méthodes.
* Les méthodes d’interface d’entité ne doivent pas définir plusieurs paramètres.
* Les méthodes d’interface d'identité doivent renvoyer `void`, `Task`ou `Task<T>` où `T` correspond à une valeur de retour.
* Les interfaces d’entité doivent avoir une classe d’implémentation concrète dans le même assembly (à savoir, la classe d’entité).

Si l’une de ces règles n’est pas respectée, une `InvalidOperationException` interviendra lors de l'exécution. Le message d’exception indique la règle non respectée.

> [!NOTE]
> Les API `SignalEntityAsync` représentent des opérations unidirectionnelles. Si une interface d’entité renvoie `Task<T>`, la valeur du paramètre `T` est systématiquement null ou `default`.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>Paramètres host.json

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence sur l'API HTTP intégrée pour la gestion des instances](durable-functions-http-api.md)