---
title: Fonctions durables afficher un aperçu des fonctionnalités - Azure Functions
description: Découvrez les fonctionnalités en version préliminaire pour fonctions durables.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596086"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 preview (Azure Functions)

*Fonctions durables* est une extension d[’Azure Functions](../functions-overview.md) et d[’Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) qui vous permet d’écrire des fonctions avec état dans un environnement sans serveur. L’extension gère l’état, les points de contrôle et les redémarrages à votre place. Si vous n’êtes pas déjà familiarisé avec les fonctions durables, consultez le [documentation vue d’ensemble](durable-functions-overview.md).

Fonctions durables est une fonctionnalité générale (à la disposition générale) d’Azure Functions, mais il contient également plusieurs sous-composants qui sont actuellement en version préliminaire publique. Cet article décrit les fonctionnalités qui vient d’être lancé en version préliminaire et sont stockées dans les détails sur leur fonctionnement et comment vous pouvez commencer à les utiliser.

> [!NOTE]
> Ces fonctionnalités en version préliminaire font partie d’une version Durable Functions 2.0, qui est actuellement un **qualité alpha de la version** avec plusieurs modifications avec rupture. Azure Functions Durable génère de package d’extension sont accessibles sur nuget.org avec les versions sous la forme de **2.0.0-alpha**. Ces builds ne conviennent pas pour des charges de travail de production, et les versions ultérieures peuvent contenir des modifications avec rupture supplémentaires.

## <a name="breaking-changes"></a>Changements cassants

Plusieurs modifications avec rupture sont introduites dans Durable Functions 2.0. Applications existantes ne sont pas prévues pour être compatible avec Durable Functions 2.0 sans modification du code. Cette section répertorie certaines des modifications :

### <a name="dropping-net-framework-support"></a>La prise en charge de .NET Framework

Prise en charge de .NET Framework (et par conséquent Functions 1.0) a été supprimé pour Durable Functions 2.0. La raison principale est de permettre des contributeurs non Windows pour facilement créer et tester des modifications aux fonctions durables à partir des plateformes macOS et Linux. La raison secondaire est d’aider à encourager les développeurs à déplacer vers la dernière version du runtime Azure Functions.

### <a name="hostjson-schema"></a>Schéma de Host.JSON

L’extrait suivant montre le nouveau schéma de host.json. Le principal changement à connaître est le nouveau `"storageProvider"` section et le `"azureStorage"` section situé en dessous. Cette modification a été effectuée pour prendre en charge [autres fournisseurs de stockage](durable-functions-preview.md#alternate-storage-providers).

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

Durable Functions 2.0 continue de se stabiliser, davantage de modifications est introduites pour la `durableTask` section host.json. Pour plus d’informations sur ces modifications, consultez [ce problème GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/641).

### <a name="public-interface-changes"></a>Modifications de l’interface publique

Les divers objets de « contexte » pris en charge par fonctions durables avaient prévus pour une utilisation dans les tests unitaires de classes de base abstraites. Dans le cadre de Durable Functions 2.0, ces classes de base abstraites ont été remplacés par les interfaces. Code de fonction qui utilise les types concrets directement ne sont pas affectés.

Le tableau suivant représente les principales modifications :

| Ancien type | Nouveau type |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Dans le cas où une classe de base abstraite contenus méthodes virtuelles, ces méthodes virtuelles ont été remplacées par les méthodes d’extension définies dans `DurableContextExtensions`.

## <a name="entity-functions"></a>Fonctions de l’entité

Fonctions de l’entité définissent des opérations pour la lecture et de petites parties d’état, connu sous le nom de la mise à jour *entités durables*. Comme les fonctions d’orchestrateur, fonctions de l’entité sont des fonctions ayant un type spécial de déclencheur, *déclencheur de l’entité*. Contrairement aux fonctions d’orchestrator, les fonctions de l’entité n’ont pas de toutes les contraintes spécifiques de code. Fonctions de l’entité également gérer l’état explicitement plutôt qu’implicitement représentant l’état via le flux de contrôle.

Le code suivant est un exemple d’une fonction d’entité simple qui définit un *compteur* entité. La fonction définit trois opérations, `add`, `subtract`, et `reset`, chacun de laquelle mettre à jour une valeur entière, `currentValue`.

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

Entité *instances* sont accessibles via un identificateur unique, le *l’ID d’entité*. Un ID d’entité est simplement une paire de chaînes qui identifie de façon unique une instance d’entité. Elle comprend :

1. un **nom de l’entité**: un nom qui identifie le type de l’entité (par exemple, « Compteur »)
2. un **clé d’entité**: chaîne qui identifie de façon unique l’entité parmi toutes les autres entités du même nom (par exemple, un GUID)

Par exemple, un *compteur* fonction d’entité peut-être être utilisée pour calculer les points dans un jeu en ligne. Chaque instance du jeu aura un ID d’entité unique, tel que `@Counter@Game1`, `@Counter@Game2`, et ainsi de suite.

### <a name="comparison-with-virtual-actors"></a>Comparaison avec les acteurs virtuels

La conception d’entités Durable, fortement influencée par le [modèle d’acteur](https://en.wikipedia.org/wiki/Actor_model). Si vous êtes déjà familiarisé avec les acteurs, les concepts qui sous-tendent les entités durables doivent être familières. En particulier, les entités durables sont similaires aux [acteurs virtuels](https://research.microsoft.com/en-us/projects/orleans/) de plusieurs façons :

* Entités durables sont adressables via un *l’ID d’entité*.
* Entité durable opérations s’exécutent en série, un à la fois, afin d’éviter des conditions de concurrence.
* Entités durables sont créées automatiquement lorsqu’ils sont appelés ou signalés.
* Lorsque vous n'exécutez pas les opérations, les entités durables sont en mode silencieux déchargées de la mémoire.

Il existe des différences importantes, toutefois, qui sont à noter :

* Entités durables sont modélisées en tant que fonctions pures. Cette conception est différente de la plupart des infrastructures et orienté objet qui représentent les acteurs à l’aide de la prise en charge spécifique au langage pour les classes, propriétés et méthodes.
* Hiérarchiser les entités durables *durabilité* sur *latence*et par conséquent, peut ne pas convenir pour les applications avec les besoins de latence stricts.
* Les messages envoyés entre les entités sont remis fiable et dans l’ordre.
* Entités durables peuvent être utilisées conjointement avec des orchestrations durables et peuvent servir de verrous distribués, qui sont décrites plus loin dans cet article.
* Modèles de demande/réponse dans les entités sont limités aux orchestrations. Pour la communication de l’entité de l’entité, seuls les messages à sens unique (également appelé « signalisation ») sont autorisés, comme dans le modèle d’acteur d’origine. Ce comportement empêche les blocages distribués.

### <a name="durable-entity-apis"></a>API Entity durable

Prise en charge de l’entité implique plusieurs API. Premièrement, il existe une nouvelle API pour la définition des fonctions de l’entité, comme indiqué ci-dessus, qui spécifient ce qui doit se produire lorsqu’une opération est appelée sur une entité. En outre, les API existantes pour les clients et les orchestrations ont été mis à jour avec de nouvelles fonctionnalités pour l’interaction avec les entités.

### <a name="implementing-entity-operations"></a>Implémentation des opérations d’entité

L’exécution d’une opération sur une entité peut appeler ces membres sur l’objet de contexte (`IDurableEntityContext` dans .NET) :

* **OperationName**: Obtient le nom de l’opération.
* **GetInput\<T >**: Obtient l’entrée pour l’opération.
* **GetState\<T >**: Obtient l’état actuel de l’entité.
* **SetState**: met à jour l’état de l’entité.
* **SignalEntity**: envoie un message unidirectionnel à une entité.
* **Self**: Obtient l’ID de l’entité.
* **Retourner**: retourne une valeur pour le client ou d’une orchestration qui a appelé l’opération.
* **IsNewlyConstructed**: retourne `true` si l’entité n’existait pas avant l’opération.
* **DestructOnExit**: supprime l’entité après avoir terminé l’opération.

Les opérations sont moins restreintes que les orchestrations :

* Opérations peuvent appeler externe d’e/s, à l’aide des API synchrones ou asynchrones (nous vous recommandons d’utiliser ceux asynchrone uniquement).
* Opérations peuvent être non déterministe. Par exemple, il est déconseillé d’appeler `DateTime.UtcNow`, `Guid.NewGuid()` ou `new Random()`.

### <a name="accessing-entities-from-clients"></a>L’accès aux entités à partir de clients

Entités durables peuvent être appelées à partir de fonctions ordinaires via le `orchestrationClient` liaison (`IDurableOrchestrationClient` dans .NET). Les méthodes suivantes sont prises en charge :

* **ReadEntityStateAsync\<T >**: lit l’état d’une entité.
* **SignalEntityAsync**: envoie un message unidirectionnel à une entité et attend qu’elle soit en file d’attente.

Ces méthodes hiérarchiser des performances au fil de la cohérence : `ReadEntityStateAsync` peut retourner une valeur périmée, et `SignalEntityAsync` peut retourner avant que l’opération ait terminé. En revanche, il est fortement cohérente d’appel des entités à partir d’orchestrations (comme décrit ci-dessous).

### <a name="accessing-entities-from-orchestrations"></a>L’accès aux entités à partir d’orchestrations

Orchestrations peuvent accéder à des entités à l’aide de l’objet de contexte. Ils peuvent choisir entre une communication unidirectionnelle (déclenché et oublié) et une communication bidirectionnelle (requête et réponse). Les méthodes correspondantes sont

* **SignalEntity**: envoie un message unidirectionnel à une entité.
* **CallEntityAsync**: envoie un message à une entité et attend une réponse indiquant que l’opération est terminée.
* **CallEntityAsync\<T >**: envoie un message à une entité et attend une réponse qui contient un résultat de type T.

Lorsque vous utilisez une communication bidirectionnelle, toutes les exceptions levées pendant l’exécution de l’opération sont également transmises à l’orchestration d’appel et levée de nouveau. En revanche, lorsque vous utilisez « fire-et-forget », les exceptions ne sont pas observées.

### <a name="locking-entities-from-orchestrations"></a>Verrouillage des entités à partir d’orchestrations

Orchestrations peuvent verrouiller des entités. Cette fonctionnalité fournit un moyen simple d’éviter les concurrences indésirables à l’aide de *sections critiques*.

L’objet de contexte fournit les méthodes suivantes :

* **LockAsync**: acquiert des verrous sur une ou plusieurs entités.
* **IsLocked**: retourne la valeur true si actuellement dans une section critique, false sinon.

La section critique se termine et tous les verrous sont libérés lorsque l’orchestration se termine. Dans .NET, `LockAsync` retourne un `IDisposable` qui met fin à la section critique lors de la suppression, ce qui peut être utilisée avec un `using` clause pour obtenir une représentation syntaxique de la section critique.

Par exemple, envisagez une orchestration qui a besoin pour tester si deux joueurs sont disponibles et attribuez-les à la fois à un jeu. Cette tâche peut être implémentée à l’aide d’une section critique comme suit :

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

Dans la section critique, les deux entités de lecteur sont verrouillées, ce qui signifie qu’ils n’exécutent pas toutes les opérations autres que celles qui sont appelés à partir de la section critique). Ce comportement empêche la concurrence avec les opérations en conflit, tels que des lecteurs assignées à un autre jeu ou signature désactivé.

Que nous imposons à plusieurs restrictions sur les sections critiques comment peut être utilisé. Ces restrictions servent à empêcher les blocages et réentrance.

* Les sections critiques ne peuvent pas être imbriquées.
* Il est impossible de créer des sections critiques suborchestrations.
* Les sections critiques peuvent appeler uniquement les entités qu’ils ont verrouillé.
* Impossible d’appeler la même entité à l’aide de plusieurs appels parallèles dans les sections critiques.
* Les sections critiques peuvent signaler uniquement les entités qu’ils n’ont pas été verrouillées.

## <a name="alternate-storage-providers"></a>Fournisseurs de stockage secondaire

Durable Task Framework prend en charge plusieurs fournisseurs de stockage, y compris [stockage Azure](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), un [en mémoire émulateur](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)et d’une expérimentation [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) fournisseur. Toutefois, jusqu'à présent, l’extension tâche Durable pour Azure Functions uniquement en charge le fournisseur de stockage Azure. En commençant par Durable Functions 2.0, la prise en charge pour les fournisseurs de stockage secondaire est ajouté, en commençant par le fournisseur de Redis.

> [!NOTE]
> Durable Functions 2.0 prend uniquement en charge les fournisseurs de .NET Standard 2.0 compatible. Au moment de la rédaction, le fournisseur Azure Service Bus ne prend pas en charge .NET Standard 2.0 et n’est donc pas disponible en tant qu’un fournisseur de stockage secondaire.

### <a name="emulator"></a>Émulateur

Le [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) fournisseur est une mémoire locale, le fournisseur de stockage non durable idéales pour les scénarios de tests locales. Il peut être configuré à l’aide de ce qui suit minimale **host.json** schéma :

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

Le [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) fournisseur persiste tous les États d’orchestration à un cluster Redis configuré.

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

Le `connectionStringName` doit référencer le nom d’une variable d’environnement ou le paramètre application. Cette application paramètre ou variable d’environnement doit contenir une valeur de chaîne de connexion Redis sous la forme de *serveur : port*. Par exemple, `localhost:6379` pour la connexion à un cluster Redis local.

> [!NOTE]
> Le fournisseur de Redis est actuellement en phase expérimentale et prend uniquement en charge les applications de fonction en cours d’exécution sur un nœud unique.
