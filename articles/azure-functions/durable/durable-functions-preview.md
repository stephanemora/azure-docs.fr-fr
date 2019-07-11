---
title: Fonctionnalités Durable Functions en version préliminaire - Azure Functions
description: Découvrez les fonctionnalités de Durable Functions en version préliminaire.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 6e96c4361cf086884bb483e2268b592dac40140b
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606067"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Préversion de Durable Functions 2.0 (Azure Functions)

*Fonctions durables* est une extension d[’Azure Functions](../functions-overview.md) et d[’Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) qui vous permet d’écrire des fonctions avec état dans un environnement sans serveur. L’extension gère l’état, les points de contrôle et les redémarrages à votre place. Si vous n’êtes pas encore familiarisé avec Durable Functions, consultez la [documentation générale](durable-functions-overview.md).

Durable Functions est une fonctionnalité mise à la disposition générale d’Azure Functions, mais elle contient également plusieurs sous-composants qui sont actuellement en version préliminaire publique. Cet article décrit les fonctionnalités qui viennent d’être lancées en version préliminaire et se penche sur les détails de leur fonctionnement et la façon dont vous pouvez commencer à les utiliser.

> [!NOTE]
> Ces fonctionnalités en version préliminaire font partie de Durable Functions version 2.0, qui est actuellement en **version alpha** avec plusieurs changements cassants. Les versions du package d’extension Azure Durable Functions sont accessibles sur nuget.org avec les versions au format forme de **2.0.0-alpha**. Ces versions ne conviennent pas aux charges de travail de production, et les versions ultérieures pourraient contenir des changements cassants supplémentaires.

## <a name="breaking-changes"></a>Dernières modifications

Plusieurs changements cassants sont introduits dans Durable Functions 2.0. Les applications existantes ne devraient pas être compatibles avec Durable Functions 2.0 sans modification du code. Cette section répertorie certaines de ces modifications :

### <a name="dropping-net-framework-support"></a>Abandon de la prise en charge de .NET Framework

La prise en charge de .NET Framework (et par conséquent Functions 1.0) a été abandonnée pour Durable Functions 2.0. L’objectif principal est de permettre aux contributeurs hors Windows de facilement créer et tester des modifications à Durable Functions à partir des plateformes macOS et Linux. L’objectif secondaire est d’encourager les développeurs à migrer vers la dernière version du runtime Azure Functions.

### <a name="hostjson-schema"></a>Schéma Host.json

L’extrait de code suivant montre le nouveau schéma host.json. Le principal changement à noter est la nouvelle section `"storageProvider"`, et la section `"azureStorage"` située en dessous. Cette modification a été effectuée pour prendre en charge [d’autres fournisseurs de stockage](durable-functions-preview.md#alternate-storage-providers).

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

Quand Durable Functions 2.0 continuera à se stabiliser, d’autres modifications seront apportées à la section `durableTask` de host.json. Pour plus d’informations sur ces modifications, consultez [ce problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Modifications de l’interface publique

Les divers objets de « contexte » pris en charge par Durable Functions avaient des classes abstraites prévues pour une utilisation lors des tests unitaires. Dans le cadre de Durable Functions 2.0, ces classes de base abstraites ont été remplacées par des interfaces. Le code de fonction utilisant les types concrets directement ne sera pas affecté.

Le tableau suivant représente les principales modifications :

| Ancien type | Nouveau type |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Dans le cas où une classe de base abstraite contenait des méthodes virtuelles, ces méthodes virtuelles ont été remplacées par les méthodes d’extension définies dans `DurableContextExtensions`.

## <a name="entity-functions"></a>Fonctions d’entité

Les fonctions d’entité définissent les opérations pour la lecture et la mise à jour de petits éléments d’état, connus sous le nom *d’entités durables*. Comme les fonctions d’orchestrateur, les fonctions d’entité sont des fonctions ayant un type spécial de déclencheur, *déclencheur d’entité*. Contrairement aux fonctions d’orchestrateur, les fonctions d’entité n’ont pas de contraintes code spécifiques. Les fonctions d’entité gèrent également l’état explicitement plutôt que de représenter implicitement l’état via le flux de contrôle.

Le code suivant est un exemple de fonction d’entité simple qui définit une entité *Compteur*. La fonction définit trois opérations, `add`, `subtract`, et `reset` chacune mettant à jour une valeur entière, `currentValue`.

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

Les *instances* d’entité sont accessibles via un identificateur unique, *l’ID d’entité*. Un ID d’entité est simplement une paire de chaînes qui identifie de façon unique une instance d’entité. Elle comprend :

1. un **nom d’entité** : un nom qui identifie le type d’entité (par exemple, « Compteur »)
2. une **clé d’entité** : une chaîne qui identifie de façon unique l’entité parmi toutes les autres entités du même nom (par exemple, un GUID)

Par exemple, une fonction d’entité *Compteur* peut être utilisée pour calculer les points dans un jeu en ligne. Chaque instance du jeu aura un ID d’entité unique, tel que `@Counter@Game1`, `@Counter@Game2`, et ainsi de suite.

### <a name="comparison-with-virtual-actors"></a>Comparaison avec les acteurs virtuels

La conception d’entités durables est fortement influencée par le [modèle d’acteur](https://en.wikipedia.org/wiki/Actor_model). Si vous êtes déjà familiarisé avec les acteurs, les concepts qui sous-tendent les entités durables devraient vous être familiers. Plus spécifiquement, les entités durables sont similaires aux [acteurs virtuels](https://research.microsoft.com/en-us/projects/orleans/) de plusieurs façons :

* Les entités durables sont adressables via un *ID d’entité*.
* Les opérations sur les entités durables s’exécutent en série, une à la fois, afin d’éviter les conditions de concurrence.
* Les entités durables sont créées automatiquement lorsqu’elles sont appelées ou signalées.
* Lorsque vous n'exécutez pas d’opérations, les entités durables sont déchargées silencieusement de la mémoire.

Il existe cependant des différences importantes, qui sont à noter :

* Les entités durables sont modélisées en tant que fonctions pures. Cette conception est différente de la plupart des infrastructures orientées objet qui représentent les acteurs avec une prise en charge spécifique au langage des classes, propriétés et méthodes.
* Les entités durables donnent la priorité à la *durabilité* sur la *latence*, et peuvent par conséquent ne pas convenir aux applications avec des besoins de latence stricts.
* Les messages envoyés entre entités sont remis de façon fiable et ordonnée.
* Les entités durables peuvent être utilisées conjointement avec des orchestrations durables et peuvent servir de verrous distribués, qui sont décrits plus loin dans cet article.
* Les modèles de demande/réponse dans les entités sont limités aux orchestrations. Pour la communication d’entité à entité, seuls les messages à sens unique (également appelés « signalisation ») sont autorisés, comme dans le modèle d’acteur d’origine. Ce comportement empêche les blocages distribués.

### <a name="durable-entity-apis"></a>API d’entité durable

La prise en charge des entités implique plusieurs API. Premièrement, il existe une nouvelle API pour la définition des fonctions de l’entité, comme indiqué ci-dessus, qui spécifient ce qui doit se produire lorsqu’une opération est appelée sur une entité. En outre, les API existantes pour les clients et orchestrations ont été mises à jour avec de nouvelles fonctionnalités pour l’interaction avec les entités.

### <a name="implementing-entity-operations"></a>Implémentation des opérations d’entité

L’exécution d’une opération sur une entité peut appeler ces membres sur l’objet de contexte (`IDurableEntityContext` dans .NET) :

* **OperationName** : Obtient le nom de l’opération.
* **GetInput\<T>**  : Obtient l’entrée pour l’opération.
* **GetState\<T>**  : Obtient l’état actuel de l’entité.
* **SetState** : Met à jour l’état de l’entité.
* **SignalEntity** : envoie un message unidirectionnel à une entité.
* **Self** : Obtient l’ID de l’entité.
* **Return** : Renvoie une valeur pour le client ou l’orchestration qui a appelé l’opération.
* **IsNewlyConstructed** : Renvoie `true` si l’entité n’existait pas avant l’opération.
* **DestructOnExit** : Supprime l’entité après avoir terminé l’opération.

Les opérations sont moins restreintes que les orchestrations :

* Les opérations peuvent appeler des E/S externes à l’aide d’API synchrones ou asynchrones (nous vous recommandons d’utiliser des API asynchrones uniquement).
* Les opérations peuvent être non déterministes. Par exemple, il est sûr d’appeler `DateTime.UtcNow`, `Guid.NewGuid()` ou `new Random()`.

### <a name="accessing-entities-from-clients"></a>Accès aux entités à partir de clients

Les entités durables peuvent être appelées à partir de fonctions ordinaires via la liaison `orchestrationClient` (`IDurableOrchestrationClient` dans .NET). Les méthodes suivantes sont prises en charge :

* **ReadEntityStateAsync\<T>**  : Lit l’état d’une entité.
* **SignalEntityAsync** : Envoie un message unidirectionnel à une entité et attend qu’elle soit mise en file d’attente.

Ces méthodes donnent la priorité aux performances plutôt qu’à la cohérence : `ReadEntityStateAsync` peut renvoyer une valeur périmée, et `SignalEntityAsync` peut renvoyer un résultat avant que l’opération soit terminée. En revanche, l’appel d’entités à partir d’orchestrations (comme décrit ci-dessous) est fortement cohérent.

### <a name="accessing-entities-from-orchestrations"></a>Accès aux entités à partir d’orchestrations

Les orchestrations peuvent accéder à des entités à l’aide de l’objet de contexte. Elles peuvent choisir entre une communication unidirectionnelle (déclencher et oublier) et une communication bidirectionnelle (requête et réponse). Les méthodes correspondantes sont

* **SignalEntity** : envoie un message unidirectionnel à une entité.
* **CallEntityAsync** : Envoie un message à une entité et attend une réponse indiquant que l’opération est terminée.
* **CallEntityAsync\<T>**  : Envoie un message à une entité et attend une réponse qui contient un résultat de type T.

Lorsque vous utilisez une communication bidirectionnelle, toutes les exceptions levées pendant l’exécution de l’opération sont également transmises à l’orchestration appelante et levées de nouveau. En revanche, lorsque vous utilisez une communication de type déclencher et oublier, les exceptions ne sont pas observées.

### <a name="locking-entities-from-orchestrations"></a>Verrouillage d’entités à partir d’orchestrations

Les orchestrations peuvent verrouiller des entités. Cette fonctionnalité fournit un moyen simple d’éviter les concurrences indésirables en employant des *sections critiques*.

L’objet de contexte fournit les méthodes suivantes :

* **LockAsync** : Acquiert des verrous sur une ou plusieurs entités.
* **IsLocked** : Renvoie la valeur true s’il se trouve actuellement dans une section critique, false sinon.

La section critique se termine et tous les verrous sont libérés lorsque l’orchestration se termine. Dans .NET, `LockAsync` renvoie un `IDisposable` qui met fin à la section critique lors de sa suppression, ce qui peut être utilisé avec une clause `using` pour obtenir une représentation syntaxique de la section critique.

Par exemple, envisagez une orchestration qui a besoin de tester si deux joueurs sont disponibles et les affecter à une partie. Cette tâche peut être implémentée à l’aide d’une section critique comme suit :

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

Dans la section critique, les deux entités de joueur sont verrouillées, ce qui signifie qu’elles n’exécutent aucune opération autre que celles appelées à partir de la section critique). Ce comportement empêche la concurrence avec les opérations en conflit, par exemple l’affectation de joueurs à une autre partie ou leur déconnexion.

Nous imposons plusieurs restrictions à la façon dont les sections critiques peuvent être utilisées. Ces restrictions servent à empêcher les blocages et réentrances.

* Les sections critiques ne peuvent pas être imbriquées.
* Les sections critiques ne peuvent pas créer de sous-orchestrations.
* Les sections critiques peuvent appeler uniquement les entités qu’elles ont verrouillées.
* Les sections critiques ne peuvent pas appeler la même entité avec plusieurs appels parallèles.
* Les sections critiques peuvent signaler uniquement les entités qui n’ont pas été verrouillées.

## <a name="alternate-storage-providers"></a>Autres fournisseurs de stockage

L’infrastructure Durable Task prend en charge plusieurs fournisseurs de stockage, dont [Stockage Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), un [en émulateur en mémoire](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)et un fournisseur [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) expérimental. Toutefois, jusqu'à présent, l’extension Durable Task pour Azure Functions prenait uniquement en charge le fournisseur Stockage Azure. À compter de Durable Functions 2.0, la prise en charge d’autres fournisseurs de stockage est ajoutée, en commençant par le fournisseur Redis.

> [!NOTE]
> Durable Functions 2.0 prend uniquement en charge les fournisseurs compatibles .NET standard 2.0. Au moment de la rédaction, le fournisseur Azure Service Bus ne prenait pas en charge .NET standard 2.0 et n’était donc pas disponible en tant qu’autre fournisseur de stockage.

### <a name="emulator"></a>Émulateur

Le fournisseur [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) est un fournisseur de stockage en mémoire locale, non durable idéal pour les scénarios de tests locaux. Il peut être configuré à l’aide du schéma **host.json** minimal suivant :

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (expérimental)

Le fournisseur [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) fait persister tous les états d’orchestration dans un cluster Redis configuré.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

Le `connectionStringName` doit référencer le nom d’une variable d’environnement ou d’un paramètre d’application. Ce paramètre d’application ou cette variable d’environnement doit contenir une valeur de chaîne de connexion Redis au format *serveur:port*. Par exemple, `localhost:6379` pour la connexion à un cluster Redis local.

> [!NOTE]
> Le fournisseur Redis est actuellement en phase expérimentale et prend uniquement en charge les applications de fonction s’exécutant sur un nœud unique.
