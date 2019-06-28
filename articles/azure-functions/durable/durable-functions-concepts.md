---
title: Concepts techniques et modèles Durable Functions dans Azure Functions
description: Découvrez les avantages offerts par l’extension Durable Functions d’Azure Functions pour l’exécution de code avec état dans le cloud.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: 95ec6a863f951a8c26abd865041c68df333a4e38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65071329"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Concepts techniques et modèles Durable Functions (Azure Functions)

Durable Functions est une extension [d’Azure Functions](../functions-overview.md) et [d’Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Vous pouvez utiliser Durable Functions pour écrire des fonctions avec état dans un environnement serverless. L’extension gère l’état, les points de contrôle et les redémarrages à votre place. 

Cet article fournit des informations détaillées sur les comportements de l’extension Durable Functions pour Azure Functions et les modèles d’implémentation courants. Ces informations peuvent vous aider à déterminer comment utiliser Durable Functions pour relever vos défis en matière de développement.

> [!NOTE]
> Durable Functions est une extension avancée d’Azure Functions et ne convient pas à toutes les applications. Cet article suppose que vous maîtrisez parfaitement les concepts [Azure Functions](../functions-overview.md) et les défis qu’impose le développement d’applications serverless.

## <a name="patterns"></a>Modèles

Cette section décrit certains modèles d’application courants qui peuvent tirer parti de Durable Functions.

### <a name="chaining"></a>Modèle 1 : Chaînage de fonctions

Dans le modèle de chaînage de fonctions, une séquence de fonctions s’exécute dans un ordre spécifique. Dans ce modèle, la sortie d’une fonction est appliquée à l’entrée d’une autre fonction.

![Schéma de modèle de chaînage de fonctions](./media/durable-functions-concepts/function-chaining.png)

Vous pouvez utiliser Durable Functions pour implémenter le modèle de chaînage de fonctions de façon concise, comme indiqué dans l’exemple suivant :

#### <a name="c-script"></a>Script C#

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

> [!NOTE]
> Il existe des différences subtiles entre l’écriture d’une fonction durable précompilée en C# et l’écriture d’une fonction durable précompilée dans le script C# indiqué dans l’exemple. Dans une fonction précompilée en C#, les paramètres durables doivent être décorés avec leurs attributs respectifs. Il peut s’agir, par exemple, de l’attribut `[OrchestrationTrigger]` pour le paramètre `DurableOrchestrationContext`. Dans une fonction durable précompilée en C#, si les paramètres ne sont pas décorés correctement, le runtime ne peut pas injecter les variables dans la fonction, et une erreur se produit. Pour plus d’exemples, consultez les [exemples azure-functions-durable-extension sur GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Dans cet exemple, les valeurs `F1`, `F2`, `F3` et `F4` représentent les noms des autres fonctions dans l’application de fonction. Vous pouvez implémenter le flux de contrôle à l’aide de constructions de codage impératives normales. Le code s’exécute du haut vers le bas. Le code peut impliquer une sémantique de flux contrôle de langage existante, notamment des instructions conditionnelles et des boucles. Vous pouvez inclure une logique de gestion des erreurs dans des blocs `try`/`catch`/`finally`.

Vous pouvez utiliser le paramètre `context` [DurableOrchestrationContext] \(.NET\) et l’objet `context.df` (JavaScript) pour appeler d’autres fonctions par nom, passer des paramètres et retourner la sortie d’une fonction. Chaque fois que le code appelle `await` (C#) ou `yield` (JavaScript), l’infrastructure Durable Functions crée des points de contrôle de la progression de l’instance de la fonction actuelle. En cas de recyclage du processus ou de la machine virtuelle au milieu de l’exécution, l’instance de la fonction reprend à partir de l’appel à `await` ou `yield` précédent. Pour en savoir plus, consultez la section suivante, Modèle 2 : Fan out/fan in.

> [!NOTE]
> L’objet `context` dans JavaScript représente le [contexte de fonction](../functions-reference-node.md#context-object) dans son ensemble, et non uniquement le paramètre [DurableOrchestrationContext].

### <a name="fan-in-out"></a>Modèle 2 : Fan out/fan in

Dans le modèle fan out/fan in, vous exécutez plusieurs fonctions en parallèle, puis attendez que toutes ces fonctions se terminent. Un travail d’agrégation est souvent effectué sur les résultats retournés par les fonctions.

![Schéma du modèle fan out/fan in](./media/durable-functions-concepts/fan-out-fan-in.png)

Avec des fonctions normales, vous pouvez effectuer un processus fan out en configurant la fonction afin qu’elle envoie plusieurs messages vers une file d’attente. Mais le processus fan-in est beaucoup plus difficile. Dans ce cas, dans une fonction normale, vous écrivez du code pour surveiller l’achèvement des fonctions déclenchées en file d’attente et stocker les sorties des fonctions. 

L’extension Durable Functions gère ce modèle avec un code relativement simple :

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Dans le cadre du processus fan-out, la distribution s’effectue vers plusieurs instances de la fonction `F2`. Le travail est suivi à l’aide d’une liste de tâches dynamique. L’API .NET `Task.WhenAll` ou JavaScript `context.df.Task.all` est appelée pour attendre la fin de toutes les fonctions appelées. Les sorties de la fonction `F2` sont ensuite agrégées à partir de la liste de tâches dynamique puis transmises à la fonction `F3`.

La création automatique de points de contrôle qui se produit lors de l’appel à `await` ou `yield` sur `Task.WhenAll` ou `context.df.Task.all` garantit qu’un incident ou qu’un redémarrage potentiel survenu au milieu du processus ne nécessite aucun redémarrage d’une quelconque tâche déjà terminée.

### <a name="async-http"></a>Modèle 3 : API HTTP Async

Le modèle des API HTTP asynchrones résout le problème de coordination de l’état des opérations de longue durée avec des clients externes. Pour implémenter ce modèle, une méthode courante consiste à faire déclencher l’action de longue durée par un appel HTTP. Le client est ensuite redirigé vers un point de terminaison d’état que le client interroge pour savoir quand l’opération est terminée.

![Schéma du modèle d’API HTTP](./media/durable-functions-concepts/async-http-api.png)

Durable Functions fournit des API intégrées qui simplifient le code que vous écrivez pour interagir avec les exécutions de fonctions de longue durée. Les exemples de démarrage rapide de Durable Functions ([C#](durable-functions-create-first-csharp.md) et [JavaScript](quickstart-js-vscode.md)) montrent une commande REST simple permettant de démarrer de nouvelles instances de fonctions d’orchestrateur. Lorsqu’une instance démarre, l’extension expose des API HTTP webhook qui interrogent l’état de la fonction d’orchestrateur. 

L’exemple suivant montre les commandes REST permettant de démarrer un orchestrateur et d’interroger son état. Pour plus de clarté, certains détails ont été retirés de l’exemple.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Étant donné que le runtime de Durable Functions gère l’état, vous n’avez pas à implémenter votre propre mécanisme de suivi de l’état.

L’extension Durable Functions inclut des webhooks intégrés qui gèrent les orchestrations à longue durée. Vous pouvez implémenter ce modèle vous-même à l’aide de vos propres déclencheurs de fonction (par exemple, HTTP, une file d’attente ou Azure Event Hubs) et de la liaison `orchestrationClient`. Ainsi, vous pouvez utiliser un message de file d’attente pour déclencher l’arrêt. Sinon, vous pouvez vous servir d’un déclencheur HTTP protégé par une stratégie d’authentification Azure Active Directory à la place des webhooks intégrés qui utilisent une clé générée pour l’authentification.

Voici quelques exemples d’utilisation du modèle d’API HTTP :

#### <a name="c"></a>C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

Dans .NET, le paramètre [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` est une valeur provenant de la liaison de sortie `orchestrationClient`, qui fait partie de l’extension Durable Functions. En JavaScript, cet objet est retourné en appelant `df.getClient(context)`. Ces objets fournissent des méthodes pour démarrer, terminer et interroger des instances de fonctions d’orchestrateur nouvelles ou existantes ou leur envoyer des événements.

Dans les exemples précédents, une fonction déclenchée par HTTP utilise une valeur `functionName` provenant de l’URL entrante et transmet cette valeur à [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). L’API de liaison [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) retourne ensuite une réponse qui contient un en-tête `Location` et des informations supplémentaires sur l’instance. Vous pouvez utiliser ces informations ultérieurement pour rechercher l’état de l’instance démarrée ou y mettre fin.

### <a name="monitoring"></a>Modèle 4 : Surveiller

Le modèle de surveillance fait référence à un processus souple et récurrent dans un flux de travail. Il peut s’agir, par exemple, d’une interrogation se poursuivant jusqu’à ce que certaines conditions soient remplies. Vous pouvez utiliser un [déclencheur de minuteur](../functions-bindings-timer.md) standard pour un scénario simple, comme une tâche de nettoyage périodique, mais son intervalle est statique et la gestion de la durée de vie des instances devient complexe. Vous pouvez utiliser Durable Functions pour créer des intervalles de récurrence flexibles, gérer la durée de vie des tâches et créer plusieurs processus de surveillance à partir d’une seule orchestration.

L’inversion du scénario d’API HTTP asynchrone antérieur représente un exemple du modèle de surveillance. Au lieu d’exposer un point de terminaison d’un client externe pour surveiller une opération longue, l’analyse de longue durée consomme un point de terminaison externe, puis attend un changement d’état.

![Schéma du modèle de surveillance](./media/durable-functions-concepts/monitor.png)

Avec quelques lignes de code, vous pouvez utiliser Durable Functions pour créer plusieurs moniteurs qui observent des points de terminaison arbitraires. Les moniteurs peuvent mettre fin à une exécution lorsqu’une condition est remplie, ou [DurableOrchestrationClient](durable-functions-instance-management.md) peut arrêter les moniteurs. Vous pouvez modifier l’intervalle `wait` d’un moniteur selon une condition spécifique (par exemple, avec un backoff exponentiel). 

Le code suivant implémente un moniteur de base :

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

Quand une requête est reçue, une nouvelle instance d’orchestration est créée pour cet ID de tâche. L’instance interroge un état jusqu’à ce qu’une condition soit respectée et que vous quittiez la boucle. Un minuteur durable contrôle la fréquence d’interrogation. Des opérations supplémentaires peuvent ensuite être exécutées, ou l’orchestration peut prendre fin. Quand la valeur `context.CurrentUtcDateTime` (.NET) ou `context.df.currentUtcDateTime` (JavaScript) dépasse la valeur `expiryTime`, le moniteur se termine.

### <a name="human"></a>Modèle 5 : Interaction humaine

De nombreux processus automatisés impliquent un certain type d’interaction humaine. L’implication de personnes humaines dans un processus automatisé est complexe, car elles ne sont pas toujours aussi disponibles et réactives que les services cloud. Un processus automatisé offre cette possibilité en utilisant des délais d’expiration et une logique de compensation.

Un processus d’approbation est un exemple de processus d’entreprise impliquant une interaction humaine. L’approbation d’un manager peut être requise si une note de frais dépasse un certain montant. Si le manager n’approuve pas cette note de frais sous 72 heures (par exemple, s’il est en vacances), un processus d’escalade est déclenché pour obtenir l’approbation d’une autre personne (par exemple, le supérieur hiérarchique de ce manager).

![Schéma du modèle d’interaction humaine](./media/durable-functions-concepts/approval.png)

Vous pouvez implémenter le modèle utilisé dans cet exemple à l’aide d’une fonction d’orchestrateur. L’orchestrateur utilise un [minuteur durable](durable-functions-timers.md) pour demander l’approbation. L’orchestrateur procède à l’escalade si le délai d’expiration est atteint. L’orchestrateur attend un [événement externe](durable-functions-external-events.md), par exemple une notification générée par une interaction humaine.

Les exemples suivants créent un processus d’approbation illustrant le modèle d’interaction humaine :

#### <a name="c-script"></a>Script C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Pour créer le minuteur durable, appelez `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). La notification est reçue par `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). Ensuite, `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) est appelée pour déterminer s’il faut procéder à l’escalade (le délai d’expiration est atteint en premier) ou traiter l’approbation (approbation reçue avant la fin du délai d’expiration).

Un client externe peut remettre la notification d’événement à une fonction d’orchestrateur en attente au moyen [d’APIS HTTP intégrées](durable-functions-http-api.md#raise-event) ou de l’API [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) à partir d’une autre fonction :

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

## <a name="pattern-6-aggregator-preview"></a>Modèle 6 : Agrégateur (préversion)

Le sixième modèle repose sur l’agrégation de données d’événement sur une période au sein d’une seule *entité* adressable. Dans ce modèle, les données agrégées peuvent provenir de plusieurs sources, être transmises par lots ou être dispersées sur de longues périodes. L’agrégateur devra peut-être effectuer une action sur les données d’événement à leur arrivée, et des clients externes devront peut-être interroger les données agrégées.

![Schéma de l’agrégateur](./media/durable-functions-concepts/aggregator.png)

L’implémentation de ce modèle avec des fonctions normales sans état fait naître un défi de taille : le contrôle d’accès concurrentiel. Vous devrez non seulement vous soucier du risque de modification des mêmes données par plusieurs threads au même moment, mais également veiller à ce que l’agrégateur s’exécute sur une seule machine virtuelle à la fois.

À l’aide d’une [fonction d’entité durable](durable-functions-preview.md#entity-functions), vous pouvez implémenter ce modèle facilement par le biais d’une fonction unique.

```csharp
public static async Task Counter(
    [EntityTrigger(EntityClassName = "Counter")] IDurableEntityContext ctx)
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

Les clients peuvent empiler les *opérations* dans le cadre d’une fonction d’entité (processus également appelé « signalisation ») à l’aide de la liaison `orchestrationClient`.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

De même, les clients peuvent interroger l’état d’une fonction d’entité à l’aide des méthodes sur la liaison `orchestrationClient`.

> [!NOTE]
> Pour l’instant, les fonctions d’entité sont disponibles uniquement dans la [préversion Durable Functions 2.0](durable-functions-preview.md).

## <a name="the-technology"></a>La technologie

En arrière-plan, l’extension Durable Functions repose sur l’infrastructure [Durable Task Framework](https://github.com/Azure/durabletask), une bibliothèque open source sur GitHub utilisée pour la génération d’orchestrations de tâches durables. Tout comme Azure Functions est l’évolution serverless d’Azure WebJobs, Durable Functions est l’évolution serverless de l’infrastructure Durable Task Framework. Microsoft et d’autres organisations utilisent couramment l’infrastructure Durable Task Framework pour automatiser les processus critiques. Il convient parfaitement à l’environnement Azure Functions sans serveur.

### <a name="event-sourcing-checkpointing-and-replay"></a>Approvisionnement d’événements, création de points de contrôle et réexécution

Les fonctions d’orchestrateur conservent de façon fiable leur état d’exécution à l’aide du modèle de conception [approvisionnement d’événements](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Au lieu de stocker directement l’état actuel d’une orchestration, l’extension Durable Functions utilise un magasin d’ajout uniquement pour enregistrer toute la série d’actions exécutées par l’orchestration de la fonction. Un magasin d’ajout uniquement présente de nombreux avantages par rapport au « vidage » de l’état d’exécution complet. Ces avantages incluent l’amélioration des performances, de l’extensibilité et de la réactivité. Vous bénéficiez aussi de la cohérence finale des données transactionnelles, ainsi que de pistes d’audit et d’un historique complets. Les pistes d’audit permettent des actions de compensation fiables.

Durable Functions utilise l’approvisionnement d’événements en toute transparence. En coulisse, l’opérateur `await` (C#) ou `yield` (JavaScript) d’une fonction d’orchestrateur cède le contrôle du thread orchestrateur au répartiteur Durable Task Framework. Le répartiteur valide ensuite dans le stockage toutes les actions que la fonction d’orchestrateur a planifiées (par exemple, l’appel d’une ou plusieurs fonctions enfant ou la planification d’un minuteur durable). L’action de validation transparente s’ajoute à l’historique d’exécution de l’instance d’orchestration. L’historique est stocké dans une table de stockage. L’action de validation ajoute ensuite des messages à une file d’attente pour planifier le travail réel. À ce stade, la fonction d’orchestrateur peut être déchargée de la mémoire. 

Sa facturation s’arrête si vous utilisez le plan de consommation Azure Functions. Si d’autres tâches doivent être effectuées, la fonction redémarre et son état est reconstruit.

Lorsqu’une fonction d’orchestration reçoit plus de tâches à effectuer (par exemple, un message de réponse est reçu ou un minuteur durable expire), l’orchestrateur sort à nouveau de veille et réexécute toute la fonction depuis le début afin de reconstruire l’état local. 

Si, au cours de la réexécution, le code tente d’appeler une fonction (ou effectue toute autre tâche asynchrone), l’infrastructure Durable Task Framework consulte l’historique d’exécution de l’orchestration en cours. Si elle constate que la [fonction d’activité](durable-functions-types-features-overview.md#activity-functions) a déjà été exécutée et a produit un résultat, elle réexécute le résultat de cette fonction, et le code d’orchestrateur continue de s’exécuter. La réexécution se poursuit jusqu’à ce que le code de la fonction s’achève ou jusqu’à ce qu’il ait planifié une nouvelle tâche asynchrone.

### <a name="orchestrator-code-constraints"></a>Contraintes du code d’orchestrateur

Le comportement de réexécution du code d’orchestrateur crée des contraintes concernant le type de code qui peut être écrit dans une fonction d’orchestrateur. Par exemple, le code d’orchestrateur doit être déterministe, car il sera réexécuté à plusieurs reprises et doit générer le même résultat à chaque fois. Pour obtenir la liste complète des contraintes, consultez [Contraintes du code d’orchestrateur](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics

L’extension Durable Functions transmet automatiquement des données de suivi structurées à [Application Insights](../functions-monitoring.md) si vous configurez votre application de fonction avec une clé d’instrumentation Azure Application Insights. Vous pouvez utiliser les données de suivi pour surveiller les actions et la progression de vos orchestrations.

Voici un exemple montrant à quoi ressemblent les événements de suivi Durable Functions dans le portail Application Insights, lorsque vous utilisez [Application Insights Analytics](../../application-insights/app-insights-analytics.md) :

![Résultats de requête dans Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Vous trouverez des données structurées utiles dans le champ `customDimensions` de chaque entrée de journal. Voici l’exemple d’une entrée entièrement développée :

![Le champ customDimensions dans une requête Application Insights](./media/durable-functions-concepts/app-insights-2.png)

En raison du comportement de réexécution du répartiteur de l’infrastructure des tâches durables, attendez-vous à voir des entrées de journal redondantes pour les actions réexécutées. Les entrées de journal redondantes peuvent vous aider à comprendre le comportement de réexécution du moteur central. L’article [Diagnostics](durable-functions-diagnostics.md) montre des exemples de requêtes qui filtrent les journaux d’activité de réexécution pour n’afficher que les journaux d’activité « en temps réel ».

## <a name="storage-and-scalability"></a>Stockage et évolutivité

L’extension Durable Functions utilise des files d’attente, des tables et des blobs dans le service Stockage Azure pour conserver l’état de l’historique d’exécution et déclencher l’exécution d’une fonction. Vous pouvez utiliser le compte de stockage par défaut pour l’application de fonction ou configurer un compte de stockage distinct. Vous pouvez choisir un compte distinct selon les limites de débit de stockage. Le code d’orchestrateur que vous écrivez n’interagit pas avec les entités de ces comptes de stockage. L’infrastructure Durable Task Framework gère les entités directement en tant que détail d’implémentation.

Les fonctions d’orchestrateur planifient les fonctions d’activité et reçoivent leurs réponses via des messages internes en file d’attente. Lorsqu’une application de fonction s’exécute dans le plan de consommation Azure Functions, le [contrôleur de mise à l’échelle Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) surveille ces files d’attente. De nouvelles instances de calcul sont ajoutées en fonction des besoins. En cas de scale out sur plusieurs machines virtuelles, une fonction d’orchestrateur peut s’exécuter sur une machine virtuelle pendant que les fonctions d’activité qu’elle appelle s’exécutent sur plusieurs machines virtuelles différentes. Pour plus d’informations sur le comportement de mise à l’échelle de Durable Functions, consultez [Performances et mise à l’échelle](durable-functions-perf-and-scale.md).

L’historique d’exécution pour les comptes d’orchestrateur est stocké dans un stockage de table. Chaque fois qu’une instance est réalimentée sur une machine virtuelle particulière, l’orchestrateur extrait son historique d’exécution du stockage de table pour reconstruire son état local. Le fait de disposer de l’historique dans le stockage de table offre un avantage appréciable : vous pouvez consulter l’historique de vos orchestrations à l’aide d’outils comme [l’Explorateur Stockage Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Les objets blob de stockage sont principalement utilisés comme un mécanisme de location pour coordonner le scale-out des instances de l’orchestration sur plusieurs machines virtuelles. Ils sont également utilisés pour contenir les données de messages volumineux qui ne peuvent pas être stockées directement dans des tables ou des files d’attente.

![Capture d’écran de l’Explorateur Stockage Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Même s’il est facile et pratique d’afficher l’historique d’exécution dans le stockage de table, ne créez pas de dépendance sur cette table. La table peut changer à mesure que l’extension Durable Functions évolue.

## <a name="known-issues"></a>Problèmes connus

Tous les problèmes connus doivent être répertoriés dans la liste [Problèmes GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Si vous rencontrez un problème qui n’apparaît pas dans GitHub, soumettez un nouveau problème. Incluez une description détaillée du problème.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Durable Functions, consultez [Durable Functions function types and features](durable-functions-types-features-overview.md) (Types de fonction et fonctionnalités de Durable Functions). 

Pour commencer :

> [!div class="nextstepaction"]
> [Créer une première fonction durable](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
