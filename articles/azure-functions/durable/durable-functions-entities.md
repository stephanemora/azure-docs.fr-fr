---
title: Entités durables - Azure Functions
description: Découvrez ce que sont les entités durables et comment les utiliser dans l’extension Durable Functions pour Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 06dfa40b6f320646513ab759f0ad5f4d10790236
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719983"
---
# <a name="entity-functions-preview"></a>Fonctions d’entité (préversion)

Les fonctions d’entité définissent les opérations pour la lecture et la mise à jour de petits éléments d’état, connus sous le nom *d’entités durables*. Comme les fonctions d’orchestrateur, les fonctions d’entité sont des fonctions ayant un type spécial de déclencheur, *déclencheur d’entité*. Contrairement aux fonctions orchestrator, les fonctions d’entité n’ont pas de contraintes de code spécifiques. Les fonctions d’entité gèrent également l’état explicitement plutôt que de représenter implicitement l’état via le flux de contrôle.

> [!NOTE]
> Les fonctions d’entité et les fonctionnalités associées sont uniquement disponibles dans Durable Functions 2.0 et versions ultérieures. Les fonctions d’entité sont actuellement en préversion publique.

## <a name="entity-identity"></a>Identité d’entité

Les entités (parfois appelées *instances* d’entité) sont accessibles par le biais d’un identificateur unique : l’*ID d’entité*. Un ID d’entité est simplement une paire de chaînes qui identifie de façon unique une instance d’entité. Elle comprend :

* un **nom d’entité** : un nom qui identifie le type d’entité (par exemple, « Compteur »).
* une **clé d’entité** : une chaîne qui identifie de façon unique l’entité parmi toutes les autres entités du même nom (par exemple, un GUID).

Par exemple, une fonction d’entité *Compteur* peut être utilisée pour calculer les points dans un jeu en ligne. Chaque instance du jeu aura un ID d’entité unique, tel que `@Counter@Game1`, `@Counter@Game2`, et ainsi de suite. Toutes les opérations qui ciblent une entité particulière nécessitent de spécifier un ID d’entité comme paramètre.

## <a name="programming-models"></a>Modèles de programmation

Les entités durables prennent en charge deux modèles de programmation différents. Le premier modèle est un modèle « fonctionnel » dynamique dans lequel l’entité est définie par une fonction unique. Le deuxième modèle est un modèle orienté objet dans lequel l’entité est définie par une classe et des méthodes. Ces modèles et les modèles de programmation permettant d’interagir avec les entités sont décrits dans les sections suivantes.

### <a name="defining-entities"></a>Définition d’entités

Il existe deux modèles de programmation facultatifs pour la création d’entités durables. Le code suivant est un exemple d’une entité de *Compteur* simple implémentée en tant que fonction standard. Cette fonction définit trois *opérations*, `add`, `reset` et `get`, chacune d’elles opérant sur une valeur d’état d’entier, `currentValue`.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
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

Ce modèle fonctionne mieux pour les implémentations d’entité simples, ou pour les implémentations qui ont un ensemble d’opérations dynamiques. Toutefois, vous pouvez aussi utiliser un modèle de programmation basé sur les classes qui est utile pour les entités qui sont statiques, mais qui ont des implémentations plus complexes. L’exemple suivant est une implémentation équivalente de l’entité `Counter` à l’aide de classes et de méthodes.

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
> La méthode de point d’entrée de la fonction avec l’attribut `[FunctionName]` *doit* être déclarée `static` lors de l’utilisation de classes d’entité. Les méthodes de point d’entrée non statiques peuvent entraîner l’initialisation de plusieurs objets et éventuellement d’autres comportements non définis.

Dans le modèle de programmation basé sur les classes, l’objet `IDurableEntityContext` est disponible dans la propriété statique `Entity.Current`.

Le modèle basé sur les classes est similaire au modèle de programmation popularisé par [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Dans ce modèle, un type d’entité est défini en tant que classe .NET. Chaque méthode de la classe est une opération qui peut être appelée par un client externe. Contrairement à Orleans, toutefois, les interfaces .NET sont facultatives. L’exemple de *Compteur* précédent n’utilisait pas d’interface, mais il peut toujours être appelé par le biais d’autres fonctions ou via des appels d’API HTTP.

> [!NOTE]
> Les fonctions de déclencheur d’entité sont disponibles dans Durable Functions 2.0 et les versions ultérieures. Actuellement, les fonctions de déclencheur d’entité sont disponibles uniquement pour les applications de fonction .NET.

### <a name="accessing-entities-from-clients"></a>Accès aux entités à partir de clients

Les entités durables peuvent être appelées ou interrogées à partir de fonctions ordinaires (également appelées *fonctions clientes*) à l’aide de la [liaison de sortie du client d’entité](durable-functions-bindings.md#entity-client). L’exemple suivant montre une fonction déclenchée par une file d’attente qui *signale* une entité à l’aide de cette liaison.

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

> [!NOTE]
> Les fonctions .NET prennent en charge les méthodes faiblement typées et de type sécurisé pour signaler des entités. Pour plus d’informations, consultez la documentation de référence sur la [liaison du client d’entité](durable-functions-bindings.md#entity-client-usage).

Le terme *signal* signifie que l’appel de l’API d’entité est unidirectionnel et asynchrone. Il n’est pas possible pour une *fonction cliente* de savoir quand l’entité a traité l’opération, ni pour une fonction d’entité de retourner une valeur à une fonction cliente. La messagerie basée sur une file d’attente unidirectionnelle était un choix de conception intentionnel pour les entités durables afin de donner la priorité à la durabilité plutôt qu’aux performances. Ce choix de conception est l’un des compromis des entités durables par rapport à d’autres technologies similaires. Actuellement, seules les orchestrations sont en mesure de gérer les valeurs de retour des entités, comme décrit dans la section suivante.

Les fonctions clientes peuvent également interroger l’état des entités, comme illustré dans l’exemple suivant :

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

Les requêtes d’état d’entité sont envoyées au magasin de suivi durable et retournent l’état *persistant* le plus récent de l’entité . Il est possible que l’état retourné soit obsolète par rapport à l’état en mémoire de l’entité. Seules les orchestrations peuvent lire l’état en mémoire d’une entité, comme décrit dans la section suivante.

### <a name="accessing-entities-from-orchestrations"></a>Accès aux entités à partir d’orchestrations

Les fonctions orchestrator peuvent accéder à des entités à l’aide d’API sur la [liaison du déclencheur d’orchestration](durable-functions-bindings.md#orchestration-trigger). Les fonctions orchestrator peuvent choisir entre une communication unidirectionnelle (« fire and forget », également appelée *signalisation*) et une communication bidirectionnelle (« requête et réponse », également appelée *appel*). L’exemple de code suivant montre une fonction orchestrator qui *appelle* et *signale* une entité *Counter* (Compteur).

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value
        await context.SignalEntityAsync<int>(entityId, "Add", 1);
    }
}
```

Seules les orchestrations peuvent appeler des entités et obtenir une réponse, qui peut être une valeur de retour ou une exception. Les fonctions clientes utilisant la[liaison cliente](durable-functions-bindings.md#entity-client) peuvent uniquement *signaler* des entités.

> [!NOTE]
> Appeler une entité à partir d’une fonction orchestrator revient à appeler une [fonction d’activité](durable-functions-types-features-overview.md#activity-functions) à partir d’une fonction orchestrator. La principale différence réside dans le fait que les fonctions d’entité sont des objets durables avec une adresse (l’*ID d’entité*) et qu’elles prennent en charge la spécification d’un nom d’opération. D’autre part, les fonctions d’activité sont sans état et n’intègrent pas le concept d’opérations.

### <a name="dependency-injection-in-entity-classes-net"></a>Injection de dépendances dans des classes d’entité (.NET)

Les classes d’entité prennent en charge l’[injection de dépendances Azure Functions](../functions-dotnet-dependency-injection.md). L’exemple suivant montre comment inscrire un service `IHttpClientFactory` dans une entité basée sur une classe.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

L’extrait de code suivant montre comment incorporer le service injecté dans votre classe d’entité.

```csharp
public class HttpEntity
{
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Contrairement à l’utilisation de l’injection de constructeur dans .NET Azure Functions standard, la méthode de point d’entrée des fonctions pour les entités basées sur des classes *doit* être déclarée `static`. La déclaration d’un point d’entrée de fonction non statique peut entraîner des conflits entre l’initialiseur d’objet Azure Functions normal et l’initialiseur d’objet Entités durables.

### <a name="bindings-in-entity-classes-net"></a>Liaisons dans des classes d’entité (.NET)

Contrairement aux fonctions normales, les méthodes de classe d’entité n’ont pas d’accès direct aux liaisons d’entrée et de sortie. Au lieu de cela, les données de liaison doivent être capturées dans la déclaration de fonction de point d’entrée, puis être passées à la méthode `DispatchAsync<T>`. Tout objet passé à `DispatchAsync<T>` est automatiquement passé dans le constructeur de classe d’entité en tant qu’argument.

L’exemple suivant montre comment une référence `CloudBlobContainer` à partir de la [liaison d’entrée d’objet blob](../functions-bindings-storage-blob.md#input) peut être rendue disponible pour une entité basée sur une classe.

```csharp
public class BlobBackedEntity
{
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Pour plus d’informations sur les liaisons dans Azure Functions, consultez la documentation [Déclencheurs et liaisons Azure Functions](../functions-triggers-bindings.md).

## <a name="entity-coordination"></a>Coordination d’entités

Il peut arriver que vous deviez coordonner des opérations entre plusieurs entités. Par exemple, dans une application bancaire, vous pouvez avoir des entités représentant des comptes bancaires individuels. Lors du transfert de fonds d’un compte vers un autre, vous devez vérifier que le compte _source_ dispose des fonds suffisants et que les mises à jour des comptes _source_ et de _destination_ sont effectuées de façon cohérente au niveau transactionnel.

### <a name="transfer-funds-example-in-c"></a>Exemple de transfert de fonds en C#

L’exemple de code suivant transfère des fonds entre deux entités de _compte_ à l’aide d’une fonction orchestrator. La coordination de mises à jour d’entités nécessite l’utilisation de la méthode `LockAsync` pour créer une _section critique_ dans l’orchestration :

> [!NOTE]
> Par souci de simplicité, cet exemple réutilise l’entité `Counter` définie précédemment. Toutefois, dans une application réelle, il serait préférable de définir plutôt une entité `BankAccount` plus détaillée.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

Dans .NET, `LockAsync` retourne un `IDisposable` qui met fin à la section critique quand elle est supprimée. Ce résultat `IDisposable` peut être utilisé avec un bloc `using` pour obtenir une représentation syntaxique de la section critique.

Dans l’exemple précédent, une fonction orchestrator a transféré des fonds d’une entité _source_ vers une entité de _destination_. La méthode `LockAsync` a verrouillé les entités de compte _source_ et de _destination_. Ce verrouillage garantissait qu’aucun autre client ne pouvait interroger ou modifier l’état de l’un des comptes tant que la logique d’orchestration n’a pas quitté la _section critique_ à la fin de l’instruction `using`. Cela avait pour effet d’empêcher la possibilité que le compte _source_ soit à découvert.

### <a name="critical-section-behavior"></a>Comportement de la section critique

La méthode `LockAsync` crée une _section critique_ dans une orchestration. Ces _sections critiques_ empêchent d’autres orchestrations d’apporter des changements faisant double emploi à un ensemble d’entités spécifié. En interne, l’API `LockAsync` envoie des opérations de « verrou » aux entités et retourne un résultat quand elle reçoit un message de réponse « verrou acquis » de chacune de ces mêmes entités. Les opérations *verrouiller* et *déverrouiller* sont toutes deux des opérations intégrées prises en charge par toutes les entités.

Aucune opération d’autres clients n’est autorisée sur une entité pendant qu’elle est verrouillée. Ce comportement garantit qu’une seule instance d’orchestration peut verrouiller une entité à la fois. Si un appelant tente d’appeler une opération sur une entité alors qu’elle est verrouillée par une orchestration, cette opération est placée dans une *file d’attente des opérations en attente*. Aucune opération en attente n’est traitée tant que l’orchestration maintenant le verrou n’a pas libéré ce dernier.

> [!NOTE] 
> Cela diffère légèrement des primitives de synchronisation utilisées dans la plupart des langages de programmation, comme l’instruction `lock` en C#. Par exemple, en C#, l’instruction `lock` doit être utilisée par tous les threads afin de garantir une synchronisation correcte entre plusieurs threads. Toutefois, les entités n’exigent pas que tous les appelants _verrouillent_ explicitement une entité. Si un appelant verrouille une entité, toutes les autres opérations sur cette entité sont bloquées et placées en file d’attente derrière ce verrou.

Les verrous sur les entités sont durables. Par conséquent, ils persistent même si le processus en cours d’exécution est recyclé. Les verrous sont conservés en interne dans le cadre de l’état durable d’une entité.

### <a name="critical-section-restrictions"></a>Restrictions relatives aux sections critiques

Nous imposons plusieurs restrictions à la façon dont les sections critiques peuvent être utilisées. Ces restrictions servent à empêcher les blocages et réentrances.

* Les sections critiques ne peuvent pas être imbriquées.
* Les sections critiques ne peuvent pas créer de sous-orchestrations.
* Les sections critiques peuvent appeler uniquement les entités qu’elles ont verrouillées.
* Les sections critiques ne peuvent pas appeler la même entité avec plusieurs appels parallèles.
* Les sections critiques peuvent signaler uniquement les entités qui n’ont pas été verrouillées.

## <a name="comparison-with-virtual-actors"></a>Comparaison avec les acteurs virtuels

La plupart des fonctionnalités des entités durables s’inspirent du [modèle d’acteur](https://en.wikipedia.org/wiki/Actor_model). Si vous êtes déjà familiarisé avec les acteurs, il se peut que vous reconnaissiez un grand nombre des concepts décrits dans cet article. Plus spécifiquement, les entités durables sont similaires aux [acteurs virtuels](https://research.microsoft.com/projects/orleans/) de plusieurs façons :

* Les entités durables sont adressables via un *ID d’entité*.
* Les opérations sur les entités durables s’exécutent en série, une à la fois, afin d’éviter les conditions de concurrence.
* Les entités durables sont créées automatiquement lorsqu’elles sont appelées ou signalées.
* Lorsque vous n'exécutez pas d’opérations, les entités durables sont déchargées silencieusement de la mémoire.

Il existe cependant des différences importantes, qui sont à noter :

* Les entités durables donnent la priorité à la *durabilité* sur la *latence*, et peuvent par conséquent ne pas convenir aux applications avec des besoins de latence stricts.
* Les messages envoyés entre entités sont remis de façon fiable et ordonnée.
* Les entités durables, qui peuvent être utilisées conjointement avec des orchestrations durables, prennent en charge les mécanismes de verrouillage distribués.
* Les modèles de demande/réponse dans les entités sont limités aux orchestrations. Pour la communication *de client à entité* et *d’entité à entité*, seule la messagerie unidirectionnelle (également appelée « signalisation ») est autorisée, comme dans le modèle d’acteur d’origine. Ce comportement empêche les blocages distribués.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les hubs de tâches](durable-functions-task-hubs.md)
