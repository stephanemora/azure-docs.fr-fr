---
title: Liaisons pour Fonctions durables - Azure
description: Guide pratique pour utiliser des déclencheurs et des liaisons pour l’extension Fonctions durables pour Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 370e6e2c569aaf6d9289bddccde2174b4dd2ee97
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Liaisons pour Fonctions durables (Azure Functions)

L’extension [Fonctions durables](durable-functions-overview.md) introduit deux nouvelles liaisons de déclencheur qui contrôlent l’exécution des fonctions d’orchestrateur et d’activité. Elle introduit également une liaison de sortie qui agit comme un client pour l’exécution de Fonctions durables.

## <a name="orchestration-triggers"></a>Déclencheurs d’orchestration

Le déclencheur d’orchestration vous permet de créer des fonctions d’orchestrateur durables. Ce déclencheur prend en charge le démarrage de nouvelles instances de fonctions d’orchestrateur et la reprise d’instances de fonctions d’orchestrateur existantes « en attente » d’une tâche.

Lorsque vous utilisez les outils Visual Studio pour Azure Functions, le déclencheur d’orchestration est configuré à l’aide de l’attribut .NET [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html).

Lorsque vous écrivez des fonctions d’orchestrateur dans des langages de script (par exemple, dans le portail Azure), le déclencheur d’orchestration est défini par l’objet JSON suivant dans le tableau `bindings` du fichier *function.json* :

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
> Les fonctions d’orchestrateur ne doivent jamais utiliser une liaison d’entrée ou de sortie autre que la liaison du déclencheur d’orchestration. Cela pourrait entraîner des problèmes avec l’extension Tâche durable car ces liaisons risquent de ne pas respecter les règles de thread unique et d’E/S.

### <a name="trigger-usage"></a>Utilisation du déclencheur

La liaison du déclencheur d’orchestration prend en charge à la fois les entrées et les sorties. Voici quelques éléments à connaître concernant la gestion des entrées et des sorties :

* **entrées** : les fonctions d’orchestration prennent uniquement en charge le type de paramètre [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Les entrées de désérialisation directement dans la signature de la fonction ne sont pas prises en charge. Le code doit utiliser la méthode [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1) pour extraire des entrées de la fonction d’orchestrateur. Ces entrées doivent être de type JSON sérialisable.
* **sorties** : les déclencheurs d’orchestration prennent en charge les valeurs de sortie ainsi que les entrées. La valeur de retour de la fonction sert à affecter la valeur de sortie et doit être de type JSON sérialisable. Si une fonction retourne `Task` ou `void`, une valeur `null` sera enregistrée comme sortie.

### <a name="trigger-sample"></a>Exemple de déclencheur

Voici l’exemple d’une simple fonction d’orchestrateur « Hello World » :

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (fonctions v2 uniquement)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

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

#### <a name="javascript-functions-v2-only"></a>JavaScript (fonctions v2 uniquement)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivityAsync("SayHello", name);
    return result;
});
```

## <a name="activity-triggers"></a>Déclencheurs d’activité

Le déclencheur d’activité vous permet de créer des fonctions appelées par des fonctions d’orchestrateur.

Si vous utilisez Visual Studio, le déclencheur d’activité est configuré à l’aide de l’attribut .NET [ActvityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html). 

Si vous utilisez le portail Azure pour le développement, le déclencheur d’activité est défini par l’objet JSON suivant dans le tableau `bindings` de *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` est le nom de l’activité. Il s’agit de la valeur qu’utilisent les fonctions d’orchestrateur pour appeler cette fonction d’activité. Cette propriété est facultative. Si cette valeur n’est pas spécifiée, le nom de la fonction est utilisé.

En interne, cette liaison de déclencheur interroge une file d’attente dans le compte de stockage par défaut pour l’application de la fonction. Cette file d’attente est un détail d’implémentation interne de l’extension, et elle n’est donc pas explicitement configurée dans les propriétés de liaison.

### <a name="trigger-behavior"></a>Comportement du déclencheur

Voici quelques remarques concernant le déclencheur d’activité :

* **Threading** : contrairement au déclencheur d’orchestration, les déclencheurs d’activité ne sont soumis à aucune restriction au niveau du thread ou des opérations d’E/S. Ils peuvent être traités comme des fonctions normales.
* **Gestion des messages incohérents** : les déclencheurs d’activité ne prennent en charge aucun message incohérent.
* **Visibilité des messages** : les messages du déclencheur d’activité sont supprimés de la file d’attente et restent invisibles pour une durée configurable. La visibilité de ces messages est renouvelée automatiquement tant que l’application de la fonction est en cours d’exécution et saine.
* **Valeurs de retour** : les valeurs de retour sont sérialisées au format JSON et conservées dans la table d’historique d’orchestration du stockage Azure Table.

> [!WARNING]
> Le serveur principal de stockage pour les fonctions de l’activité est un détail d’implémentation, et le code utilisateur ne doit pas interagir directement avec ces entités de stockage.

### <a name="trigger-usage"></a>Utilisation du déclencheur

La liaison du déclencheur d’activité prend en charge à la fois les entrées et les sorties, exactement comme le déclencheur d’orchestration. Voici quelques éléments à connaître concernant la gestion des entrées et des sorties :

* **entrées** : les fonctions d’activité natives utilisent le type de paramètre [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html). Une fonction d’activité peut également être déclarée avec n’importe quel type de paramètre sérialisable au format JSON. Lorsque vous utilisez `DurableActivityContext`, vous pouvez appeler [GetInput\<T>](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) pour extraire et désérialiser l’entrée de la fonction d’activité.
* **sorties** : les fonctions d’activité prennent en charge les valeurs de sortie ainsi que les entrées. La valeur de retour de la fonction sert à affecter la valeur de sortie et doit être de type JSON sérialisable. Si une fonction retourne `Task` ou `void`, une valeur `null` sera enregistrée comme sortie.
* **métadonnées** : les fonctions d’activité peuvent être liées à un paramètre `string instanceId` pour obtenir l’ID d’instance de l’orchestration parente.

### <a name="trigger-sample"></a>Exemple de déclencheur

Voici l’exemple d’une simple fonction d’activité « Hello World » :

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (fonctions v2 uniquement)

```javascript
module.exports = function(context) {
    context.done(null, `Hello ${context.bindings.name}!`);
};
```

Le type de paramètre par défaut pour la liaison `ActivityTriggerAttribute` est `DurableActivityContext`. Cependant, les déclencheurs d’activité prennent également en charge la liaison directe avec des types de paramètres sérialisables au format JSON (notamment les types primitifs). La même fonction peut donc être simplifiée comme suit :

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (fonctions v2 uniquement)

```javascript
module.exports = function(context, name) {
    context.done(null, `Hello ${name}!`);
};
```

### <a name="passing-multiple-parameters"></a>Transmission de plusieurs paramètres 

Il n’est pas possible de transmettre directement plusieurs paramètres à une fonction d’activité. Dans ce cas, il est recommandé de les transmettre dans un tableau d’objets ou d’utiliser des objets [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples).

Dans l’exemple suivant, de nouvelles fonctionnalités des objets [ValueTuples](https://docs.microsoft.com/dotnet/csharp/tuples) sont ajoutées avec [C# 7](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7#tuples) :

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<dynamic> RunOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    dynamic courseRecommendations = await context.CallActivityAsync<dynamic>("CourseRecommendations", (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<dynamic> Mapper([ActivityTrigger] DurableActivityContext inputs)
{
    // parse input for student's major and year in university 
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

## <a name="orchestration-client"></a>Client d’orchestration

La liaison du client d’orchestration vous permet d’écrire des fonctions qui interagissent avec les fonctions d’orchestrateur. Par exemple, vous pouvez agir sur les instances de l’orchestration de l’une des manières suivantes :
* Les démarrer.
* Interroger leur état.
* Les arrêter.
* Leur envoyer des événements pendant qu’elles sont exécutées.

Si vous utilisez Visual Studio, vous pouvez établir une liaison avec le client d’orchestration en utilisant l’attribut .NET [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html).

Si vous utilisez des scripts de langage (par exemple, des fichiers *.csx*) pour le développement, le déclencheur d’orchestration est défini par l’objet JSON suivant dans le tableau `bindings` du fichier *function.json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "out"
}
```

* `taskHub` : utilisé dans les scénarios où plusieurs applications de fonction partagent le même compte de stockage, mais doivent être isolées les unes des autres. Si ce champ n’est pas spécifié, la valeur `host.json` sera utilisée par défaut. Cette valeur doit correspondre à la valeur utilisée par les fonctions d’orchestrateur cible.
* `connectionName` : nom d’un paramètre d’application contenant une chaîne de connexion du compte de stockage. Le compte de stockage représenté par cette chaîne de connexion doit être le même que celui utilisé par les fonctions d’orchestrateur cible. Si elle n’est pas spécifiée, la chaîne de connexion du compte de stockage par défaut pour l’application de la fonction est utilisée.

> [!NOTE]
> Dans la plupart des cas, nous vous recommandons d’omettre ces propriétés et de vous appuyer sur le comportement par défaut.

### <a name="client-usage"></a>Utilisation du client

Dans les fonctions C#, vous établissez généralement une liaison avec `DurableOrchestrationClient`, ce qui vous donne un accès total à tous les clients API pris en charge par Fonctions durables. Les API sur l’objet client incluent :

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)

Vous pouvez également établir une liaison avec `IAsyncCollector<T>`, où `T` est [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) ou `JObject`.

Consultez la documentation de l’API [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) pour plus d’informations sur ces opérations.

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
      "direction": "out"
    }
  ],
  "disabled": false
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

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null, id);
};
```

Vous trouverez plus d’informations sur le démarrage des instances dans la section [Gestion d’instance](durable-functions-instance-management.md).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la création de points de contrôle et les comportements de réexécution](durable-functions-checkpointing-and-replay.md)

