---
title: Les modèles de fonctions durables et concepts techniques dans Azure Functions
description: Découvrez comment l’extension fonctions durables dans Azure Functions vous offre les avantages de l’exécution du code avec état dans le cloud.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: aa9563266f6b43e3bc2f21fbc0b340c86c5895ae
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608729"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Les modèles de fonctions durables et concepts techniques (Azure Functions)

Fonctions durables est une extension de [Azure Functions](../functions-overview.md) et [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Vous pouvez utiliser les fonctions durables pour écrire des fonctions avec état dans un environnement sans serveur. L’extension gère l’état, les points de contrôle et les redémarrages à votre place. 

Cet article propose vous des informations détaillées sur les comportements de l’extension fonctions durables pour Azure Functions et les modèles de mise en œuvre courants. Les informations peuvent vous aider à déterminer comment utiliser les fonctions durables pour aider à résoudre vos défis de développement.

> [!NOTE]
> Fonctions durables est une extension avancée pour Azure Functions qui n’est pas approprié pour toutes les applications. Cet article suppose que vous avez une bonne connaissance des concepts dans [Azure Functions](../functions-overview.md) et les défis impliqués dans le développement d’applications sans serveur.

## <a name="patterns"></a>Modèles

Cette section décrit certains modèles d’application courants où les fonctions durables peut être utiles.

### <a name="chaining"></a>Modèle 1 : Chaînage de fonctions

Dans la modèle de chaînage de fonction, une séquence de fonctions s’exécute dans un ordre spécifique. Dans ce modèle, la sortie d’une fonction est appliquée à l’entrée d’une autre fonction.

![Un diagramme de la fonction de modèle de chaînage](./media/durable-functions-concepts/function-chaining.png)

Vous pouvez utiliser les fonctions durables pour implémenter la fonction de chaînage de modèle de façon concise comme indiqué dans l’exemple suivant :

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
> Il existe des différences subtiles entre l’écriture d’une fonction durable précompilée C# et l’écriture d’une fonction durable précompilée le C# script qui est indiqué dans l’exemple. Dans un C# précompilé (fonction), paramètres durables doivent être décorées avec attributs respectifs. Par exemple, le `[OrchestrationTrigger]` d’attribut pour le `DurableOrchestrationContext` paramètre. Dans un C# précompilé fonction durable, si les paramètres ne sont pas décorées correctement, le runtime ne peut pas injecter les variables dans la fonction, et une erreur se produit. Pour plus d’exemples, consultez le [azure-fonctions-durable-extension des exemples sur GitHub](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Dans cet exemple, les valeurs `F1`, `F2`, `F3`, et `F4` sont les noms des autres fonctions dans l’application de fonction. Vous pouvez implémenter des flux de contrôle à l’aide de constructions de code impératives normales. Code s’exécute à partir du haut vers le bas. Le code peut impliquer existant sémantique de flux du contrôle de langage, tels que les conditions et boucles. Vous pouvez inclure dans une logique de gestion des erreurs `try` / `catch` / `finally` blocs.

Vous pouvez utiliser la `context` paramètre [DurableOrchestrationContext] \(.NET\) et `context.df` , objet (JavaScript) pour appeler d’autres fonctions par nom, de transmettre des paramètres et de retourner (fonction) sortie. Chaque fois que le code appelle `await` (C#) ou `yield` (JavaScript), les points de contrôle de framework de fonctions durables la progression de l’instance actuelle de la fonction. Si la machine virtuelle ou le processus est recyclé au milieu de l’exécution, l’instance de la fonction reprend à partir de l’exemple précédent `await` ou `yield` appeler. Pour plus d’informations, consultez la section suivante, le modèle #2 : Ventilateur hors/ventilateur dans.

> [!NOTE]
> Le `context` objet dans JavaScript représente l’intégralité de [contexte de la fonction](../functions-reference-node.md#context-object), non seulement la [DurableOrchestrationContext] paramètre.

### <a name="fan-in-out"></a>Modèle 2 : Ventilateur hors/ventilateur dans

Le ventilateur hors/ventilateur dans le modèle, vous exécutez plusieurs fonctions en parallèle, puis attendez que toutes les fonctions à la fin. Souvent, un travail d’agrégation est effectué sur les résultats sont retournés par les fonctions.

![Un diagramme du ventilateur out/fan modèle](./media/durable-functions-concepts/fan-out-fan-in.png)

Avec les fonctions normales, vous pouvez déployer en ayant la fonction envoyer plusieurs messages à une file d’attente. Dans la ventilation est beaucoup plus difficile. Pour entrent dans une fonction normale, vous écrivez du code pour effectuer le suivi lors de la fin des fonctions déclenchées par la file d’attente et le stocker les sorties de fonction. 

L’extension fonctions durables gère ce modèle avec un code relativement simple :

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

Le processus fan est réparti sur plusieurs instances de la `F2` (fonction). Le travail est suivi à l’aide d’une liste dynamique de tâches. .NET `Task.WhenAll` API ou JavaScript `context.df.Task.all` API est appelée, pour attendre que toutes les fonctions appelées à terminer. Ensuite, le `F2` fonction les sorties sont agrégées à partir de la liste des tâches dynamique et passé à la `F3` (fonction).

Les points de contrôle automatique se produit à la `await` ou `yield` appeler sur `Task.WhenAll` ou `context.df.Task.all` garantit qu’un blocage au milieu ou le redémarrage ne nécessite pas le redémarrage d’une tâche est déjà terminée.

### <a name="async-http"></a>Modèle 3 : API HTTP Async

Le modèle de APIs HTTP asynchrone résout le problème de coordination de l’état des opérations à long terme avec des clients externes. Une méthode courante pour implémenter ce modèle consiste à disposer les HTTP à appeler déclencher l’action d’exécution longue. Puis, rediriger le client vers un point de terminaison d’état que le client interroge pour savoir quand l’opération est terminée.

![Un diagramme du modèle d’API HTTP](./media/durable-functions-concepts/async-http-api.png)

Fonctions durables fournit des API intégrées qui simplifient le code que vous écrivez pour interagir avec les exécutions de fonctions longues. Les exemples de démarrage rapide de fonctions durables ([ C# ](durable-functions-create-first-csharp.md) et [JavaScript](quickstart-js-vscode.md)) afficher une commande REST simple que vous pouvez utiliser pour démarrer de nouvelles instances de fonction orchestrator. Après le démarrage d’une instance, l’extension expose webhook HTTP APIs qui interrogent l’état de la fonction orchestrator. 

L’exemple suivant indique les commandes REST démarrer un orchestrateur et d’interroger son état. Pour plus de clarté, certains détails ont été retirés de l’exemple.

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

Étant donné que l’exécution de fonctions durables gère l’état, vous n’avez pas besoin d’implémenter votre propre mécanisme de suivi de l’état.

L’extension fonctions durables a des webhooks intégrés qui gèrent des orchestrations à long terme. Vous pouvez implémenter ce modèle vous-même à l’aide de vos propres déclencheurs de fonction (par exemple, HTTP, une file d’attente ou Azure Event Hubs) et le `orchestrationClient` liaison. Par exemple, vous pouvez utiliser un message de file d’attente pour déclencher l’arrêt. Ou bien, vous pouvez utiliser un déclencheur HTTP qui est protégé par une stratégie d’authentification Azure Active Directory au lieu de webhooks intégrés qui utilisent une clé générée pour l’authentification.

Voici quelques exemples montrant comment utiliser le modèle de l’API HTTP :

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

> [!WARNING]
> Lorsque vous développez localement dans JavaScript, pour utiliser des méthodes sur `DurableOrchestrationClient`, vous devez définir la variable d’environnement `WEBSITE_HOSTNAME` à `localhost:<port>` (par exemple, `localhost:7071`). Pour plus d’informations sur cette exigence, consultez [GitHub problème 28](https://github.com/Azure/azure-functions-durable-js/issues/28).

Dans .NET, le paramètre [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` est une valeur provenant de la liaison de sortie `orchestrationClient`, qui fait partie de l’extension Durable Functions. En JavaScript, cet objet est retourné en appelant `df.getClient(context)`. Ces objets fournissent des méthodes que vous pouvez utiliser pour démarrer, envoyer des événements à, terminer et de requête pour les instances de fonction d’orchestrateur nouvelles ou existantes.

Dans les exemples précédents, une fonction déclenchée par HTTP utilise un `functionName` valeur à partir de l’URL entrante et transmet la valeur à [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Le [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) liaison API puis renvoie une réponse qui contient un `Location` en-tête et des informations supplémentaires sur l’instance. Vous pouvez utiliser les informations ultérieurement pour rechercher l’état de l’instance démarrée ou pour mettre fin à l’instance.

### <a name="monitoring"></a>Modèle 4 : Surveiller

Le modèle de surveillance fait référence à un processus souple, périodique dans un flux de travail. Un exemple est l’interrogation jusqu'à ce que certaines conditions sont remplies. Vous pouvez utiliser une expression régulière [déclencheur de minuteur](../functions-bindings-timer.md) pour traiter un base scénario, par exemple un travail de nettoyage périodique, mais son intervalle est statique et la gestion des durées de vie instance devient complexe. Vous pouvez utiliser les fonctions durables pour créer des intervalles de récurrence flexibles, gérer les durées de vie des tâches et créer analyse plusieurs processus à partir d’une seule orchestration.

Un exemple du modèle d’analyse consiste à inverser le scénario d’API HTTP async antérieures. Au lieu d’exposer un point de terminaison pour un client externe surveiller une opération longue, l’analyse longue consomme un point de terminaison externe, puis attend un changement d’état.

![Un diagramme du modèle d’analyse](./media/durable-functions-concepts/monitor.png)

En quelques lignes de code, vous pouvez utiliser des fonctions durables pour créer plusieurs moniteurs qui observent des points de terminaison arbitraires. Les moniteurs peut se terminer l’exécution lorsqu’une condition est remplie, ou le [DurableOrchestrationClient](durable-functions-instance-management.md) peut s’arrêter les analyses. Vous pouvez modifier d’un analyseur `wait` intervalle basé sur une condition spécifique (par exemple, une interruption exponentielle.) 

Le code suivant implémente un moniteur de base :

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

Quand une requête est reçue, une nouvelle instance d’orchestration est créée pour cet ID de tâche. L’instance interroge un état jusqu’à ce qu’une condition soit respectée et que vous quittiez la boucle. Un minuteur durable contrôle la fréquence d’interrogation. Puis, plus de travail peut être effectuée, ou l’orchestration peut se terminer. Lorsque le `context.CurrentUtcDateTime` (.NET) ou `context.df.currentUtcDateTime` (JavaScript) dépasse le `expiryTime` valeur, le moniteur se termine.

### <a name="human"></a>Modèle 5 : Interaction humaine

De nombreux processus automatisés impliquent un certain type d’interaction humaine. Des acteurs humains interagissent dans un processus automatisé sont délicat, car les gens ne sont pas comme hautement disponible et aussi réactives que les services cloud. Un processus automatisé peut autoriser pour cela à l’aide des délais d’attente et la compensation logique.

Un processus d’approbation est un exemple d’un processus d’entreprise qui implique une interaction humaine. L’approbation d’un gestionnaire peut être nécessaire pour une note de frais dépasse un certain montant. Si le gestionnaire n’approuve la note de frais dans les 72 heures (peut-être que le gestionnaire est en vacances), un processus d’escalade de verrous intervient pour obtenir l’approbation d’une autre personne (par exemple du responsable).

![Un diagramme du modèle d’interaction humaine](./media/durable-functions-concepts/approval.png)

Vous pouvez implémenter le modèle dans cet exemple à l’aide d’une fonction d’orchestrateur. L’orchestrateur utilise un [minuteur durable](durable-functions-timers.md) pour demander l’approbation. L’orchestrateur fait remonter si le délai d’expiration se produit. L’orchestrateur attend une [événement externe](durable-functions-external-events.md), par exemple une notification qui est générée par une interaction humaine.

Ces exemples créent un processus d’approbation pour illustrer le modèle d’interaction humaine :

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

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

Pour créer le minuteur durable, appelez `context.CreateTimer` (.NET) ou `context.df.createTimer` (JavaScript). La notification est reçue par `context.WaitForExternalEvent` (.NET) ou `context.df.waitForExternalEvent` (JavaScript). Ensuite, `Task.WhenAny` (.NET) ou `context.df.Task.any` (JavaScript) est appelée pour déterminer s’il faut faire remonter (délai d’expiration se produit tout d’abord) ou traiter l’approbation (l’approbation reçue avant le délai d’expiration).

Un client externe peut fournir la notification d’événement pour une fonction d’orchestrateur en attente à l’aide du [APIs HTTP intégrées](durable-functions-http-api.md#raise-event) ou à l’aide de la [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) API à partir de une autre fonction :

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

## <a name="the-technology"></a>La technologie

Dans les coulisses, l’extension fonctions durables repose sur le [Durable Task Framework](https://github.com/Azure/durabletask), une bibliothèque open source sur GitHub qui est utilisé pour générer des orchestrations de tâches durables. Par exemple, Azure Functions est l’évolution sans serveur d’Azure WebJobs, fonctions durables est l’évolution sans serveur de l’infrastructure des tâches durables. Microsoft et autres organisations utilisent largement Durable Task Framework pour automatiser les processus critiques. Il convient parfaitement à l’environnement Azure Functions sans serveur.

### <a name="event-sourcing-checkpointing-and-replay"></a>Approvisionnement d’événements, création de points de contrôle et réexécution

Les fonctions d’orchestrateur maintenir de façon fiable leur état d’exécution à l’aide de la [approvisionnement en événements](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) modèle de conception. Au lieu de stocker directement l’état actuel d’une orchestration, l’extension fonctions durables utilise un magasin d’ajout uniquement pour enregistrer la série complète d’actions prises par l’orchestration de fonction. Un magasin d’ajout uniquement présente de nombreux avantages par rapport au « vidage » de l’état d’exécution complet. Présente les avantages, l’évolutivité, performances et la réactivité accrue. Vous obtenez également la cohérence pour les données transactionnelles et de journaux d’audit complets et de l’historique. Les pistes d’audit prennent en charge les actions de compensation fiables.

Fonctions durables utilise l’événement d’approvisionnement en toute transparence. Dans les coulisses, le `await` (C#) ou `yield` opérateur (JavaScript) dans une fonction d’orchestrateur cède le contrôle du thread orchestrateur au répartiteur Durable Task Framework. Le répartiteur valide ensuite dans le stockage toutes les actions que la fonction d’orchestrateur a planifiées (par exemple, l’appel d’une ou plusieurs fonctions enfant ou la planification d’un minuteur durable). L’action de validation transparente s’ajoute à l’historique d’exécution de l’instance d’orchestration. L’historique est stocké dans une table de stockage. L’action de validation ajoute ensuite des messages à une file d’attente pour planifier le travail réel. À ce stade, la fonction d’orchestrateur peut être déchargée de la mémoire. 

Facturation de la fonction d’orchestrateur s’arrête si vous utilisez le plan de consommation Azure Functions. Quand il est plus rien à faire, le redémarrage de la fonction, et son état est reconstruit.

Quand une fonction d’orchestration reçoit plus rien à faire (par exemple, un message de réponse est reçu ou un minuteur durable expire), l’orchestrateur sort de veille et réexécute la fonction entière à partir du début à reconstruire l’état local. 

Lors de la relecture, si le code tente d’appeler une fonction (ou effectuer d’autres async de n’importe quel travail), l’infrastructure des tâches durables consulte l’historique d’exécution de l’orchestration en cours. Si elle constate que le [fonction d’activité](durable-functions-types-features-overview.md#activity-functions) déjà exécutée et a produit un résultat, elle réexécute les résultats de cette fonction et le code d’orchestrateur continue de s’exécuter. Relecture se poursuit jusqu'à ce que le code de fonction est terminé ou jusqu'à ce qu’il a planifié une nouvelle tâche asynchrone.

### <a name="orchestrator-code-constraints"></a>Contraintes du code d’orchestrateur

Le comportement de réexécution de code d’orchestrateur crée des contraintes sur le type de code que vous pouvez écrire dans une fonction d’orchestrateur. Par exemple, un code d’orchestrateur doit être déterministe, car il sera réexécuté plusieurs fois, et il doit générer le même résultat chaque fois. Pour obtenir la liste complète des contraintes, consultez [contraintes du code d’orchestrateur](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics

L’extension fonctions durables transmet automatiquement des données de suivi structurées à [Application Insights](../functions-monitoring.md) si vous configurez votre application de fonction avec une clé d’instrumentation Azure Application Insights. Vous pouvez utiliser les données de suivi pour surveiller les actions et la progression de vos orchestrations.

Voici un exemple de quoi ressemblent les fonctions durables, le suivi des événements dans le portail Application Insights lorsque vous utilisez [Application Insights Analytique](../../application-insights/app-insights-analytics.md):

![Résultats de la requête application Insights](./media/durable-functions-concepts/app-insights-1.png)

Vous trouverez de nombreuses données structurées dans le `customDimensions` champ dans chaque entrée de journal. Voici un exemple d’une entrée qui est entièrement développé :

![Le champ customDimensions dans une requête Application Insights](./media/durable-functions-concepts/app-insights-2.png)

En raison du comportement de réexécution du répartiteur de l’infrastructure des tâches durables, attendez-vous à voir des entrées de journal redondantes pour les actions réexécutées. Entrées de journal redondantes peuvent vous aider à comprendre le comportement de réexécution du moteur de base. Le [Diagnostics](durable-functions-diagnostics.md) article montre des exemples de requêtes qui filtrent les journaux de réexécution, afin de voir uniquement les journaux « en temps réel ».

## <a name="storage-and-scalability"></a>Stockage et évolutivité

L’extension fonctions durables utilise des files d’attente, tables et objets BLOB dans le stockage Azure pour conserver l’exécution historique état et déclencher l’exécution des fonctions. Vous pouvez utiliser le compte de stockage par défaut pour l’application de fonction, ou vous pouvez configurer un compte de stockage distinct. Vous souhaiterez un compte distinct en fonction des limites de débit de stockage. Le code d’orchestrateur que vous écrivez n’interagit pas avec les entités dans ces comptes de stockage. Durable Task Framework gère les entités directement comme un détail d’implémentation.

Les fonctions d’orchestrateur planifient les fonctions d’activité et reçoivent leurs réponses via des messages internes en file d’attente. Lorsqu’une application de fonction s’exécute dans le plan de consommation Azure Functions, le [contrôleur de mise à l’échelle Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) surveille ces files d’attente. Nouvelles instances de calcul sont ajoutées en fonction des besoins. Lorsqu’il est étendu à plusieurs machines virtuelles, une fonction d’orchestrateur peut s’exécuter sur une machine virtuelle, tout en fonctions d’activité qui les appels de fonction d’orchestrateur peuvent s’exécuter sur plusieurs machines virtuelles différentes. Pour plus d’informations sur le comportement de mise à l’échelle de fonctions durables, consultez [performances et évolutivité](durable-functions-perf-and-scale.md).

L’historique d’exécution pour les comptes d’orchestrateur est stockée dans le stockage table. Chaque fois qu’une instance est réalimentée sur une machine virtuelle spécifique, l’orchestrateur extrait son historique d’exécution à partir du stockage de table afin de reconstruire son état local. Un aspect pratique d’avoir l’historique n’est disponible dans le stockage table est que vous pouvez utiliser des outils tels que [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) pour consulter l’historique de vos orchestrations.

Objets BLOB de stockage est principalement utilisés comme un mécanisme de bail pour coordonner la montée en puissance des instances d’orchestration sur plusieurs machines virtuelles. Objets BLOB de stockage conserve des données pour les messages volumineux qui ne peuvent pas être stockées directement dans les tables ou files d’attente.

![Une capture d’écran de l’Explorateur de stockage Azure](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> Bien qu’il soit facile et pratique d’afficher l’historique d’exécution dans le stockage table, n’effectuez pas toutes les dépendances sur cette table. La table peut changer à mesure que l’extension fonctions durables évolue.

## <a name="known-issues"></a>Problèmes connus

Tous les problèmes connus doivent être répertoriés dans la liste [Problèmes GitHub](https://github.com/Azure/azure-functions-durable-extension/issues). Si vous rencontrez un problème et ne peut pas trouver le problème dans GitHub, ouvrez un nouveau problème. Inclure une description détaillée du problème.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctions durables, consultez [fonctions durables fonction des types et fonctionnalités](durable-functions-types-features-overview.md). 

Pour commencer :

> [!div class="nextstepaction"]
> [Créer une première fonction durable](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
