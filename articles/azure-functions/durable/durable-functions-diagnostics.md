---
title: Diagnostics dans Fonctions durables - Azure
description: Découvrez comment gérer diagnostiquer les problèmes avec l’extension Fonctions durables pour Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: ac9abaaea7f33627332a9bc7563745b5efdf3d12
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436238"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostics de Durable Functions dans Azure

Il existe plusieurs options permettant de diagnostiquer les problèmes avec [Fonctions durables](durable-functions-overview.md). Certaines de ces options sont les mêmes pour les fonctions standard, et d’autres sont uniques à Fonctions durables.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) est la méthode recommandée pour les diagnostics et la surveillance dans Azure Functions. Il en va de même pour Fonctions durables. Pour obtenir une vue d’ensemble montrant comment tirer parti d’Application Insights dans votre application de fonction, consultez [Surveiller l’exécution des fonctions Azure](../functions-monitoring.md).

L’extension Fonctions durables d’Azure émet également des *événements de suivi* vous permettant de tracer l’exécution de bout en bout d’une orchestration. Ces événements sont accessibles et interrogés à l’aide de l’outil [Application Insights Analytics](../../azure-monitor/app/analytics.md) dans le portail Azure.

### <a name="tracking-data"></a>Suivi des données

Chaque événement du cycle de vie d’une instance d’orchestration entraîne l’inscription d’un événement de suivi dans la collection des **suivis** d’Application Insights. Cet événement contient une charge utile **customDimensions** avec plusieurs champs.  Les noms de champs contiennent tous le préfixe `prop__`.

* **hubName** : Nom du hub de tâches dans lequel vos orchestrations sont en cours d’exécution.
* **appName** : Le nom de l’application de fonction. Utile si plusieurs de vos applications de fonction partagent la même instance Application Insights.
* **slotName** : [emplacement de déploiement](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) dans lequel s’exécute l’application de fonction actuelle. Utile pour tirer parti des emplacements de déploiement pour la version de vos orchestrations.
* **functionName** : Nom de la fonction d’orchestrateur ou d’activité.
* **functionType** : Type de la fonction, par exemple **Orchestrateur** ou **Activité**.
* **instanceId** : ID unique de l’instance d’orchestration.
* **state** : État d’exécution du cycle de vie de l’instance. Les valeurs valides incluent :
  * **Scheduled** : La fonction a été planifiée pour être exécutée, mais elle n’a pas encore démarré.
  * **Started** : La fonction a démarré, mais elle n’a pas encore été attendue ou s’est terminée.
  * **Awaited** : L’orchestrateur a planifié des tâches et attend qu’elles se terminent.
  * **Listening** : L’orchestrateur écoute une notification d’événement externe.
  * **Completed** : La fonction s’est terminée avec succès.
  * **Failed** : La fonction a échoué avec une erreur.
* **reason** : Données supplémentaires associées à l’événement de suivi. Par exemple, si une instance attend une notification d’événement externe, ce champ indique le nom de l’événement attendu. Si une fonction a échoué, il contient les détails de l’erreur.
* **isReplay** : Valeur booléenne qui indique si l’événement de suivi est destiné à une réexécution.
* **extensionVersion** : Version de l’extension Tâche durable. Ces données sont particulièrement importantes pour signaler d’éventuels bogues dans l’extension. Des instances à long terme peuvent signaler plusieurs versions si une mise à jour se produit pendant leur exécution.
* **sequenceNumber** : Numéro séquentiel d’extension pour un événement. Combiné avec le timestamp, il permet de classer les événements par durée d’exécution. *Notez que ce numéro sera réinitialisé à zéro si l’hôte redémarre alors que l’instance est en cours d’exécution, il est donc important de toujours d’abord trier par timestamp, puis par sequenceNumber.*

Le niveau de détail des données de suivi transmises à Application Insights peut être configuré dans la section `logger` (Functions 1.x) ou `logging` (Functions 2.x) du fichier `host.json`.

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Par défaut, tous les événements de suivi non rejoués sont transmis. Le volume de données peut être réduit en définissant `Host.Triggers.DurableTask` sur `"Warning"` ou `"Error"`. Dans ce cas, les événements de suivi seront uniquement transmis en cas de situation exceptionnelle.

Pour activer l’émission d’événements de relecture d’orchestration détaillée, `LogReplayEvents` peut être défini sur `true` dans le fichier `host.json` sous `durableTask` comme indiqué :

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Par défaut, les données de télémétrie Application Insights sont échantillonnées par le runtime Azure Functions pour éviter un transfert trop fréquent de données. Cela peut entraîner une perte des informations de suivi si de nombreux événements de cycle de vie se produisent sur une courte période. L’article sur [la surveillance d’Azure Functions](../functions-monitoring.md#configure-sampling) explique comment configurer ce comportement.

### <a name="single-instance-query"></a>Requête d’instance unique

La requête suivante affiche les données de suivi historiques d’une seule instance de la fonction d’orchestration [Séquence Hello](durable-functions-sequence.md). Elle s’écrit à l’aide du langage [Application Insights Query Language (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Elle exclut la réexécution afin que seul le chemin d’exécution *logique* s’affiche. Les événements peuvent être classés en les triant par `timestamp` et `sequenceNumber` comme indiqué dans la requête ci-dessous :

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Le résultat est une liste d’événements de suivi indiquant le chemin d’exécution de l’orchestration, y compris toutes les fonctions d’activité triées par durée d’exécution par ordre ascendant.

![Requête Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Requête de résumé de l’instance

La requête suivante affiche l’état de toutes les instances d’orchestration qui ont été exécutées dans un intervalle de temps spécifié.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Le résultat est une liste d’ID d’instances et leur actuel état d’exécution.

![Requête Application Insights](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Journalisation

Il est important de garder à l’esprit le comportement de réexécution de l’orchestrateur lors de l’écriture des journaux directement à partir d’une fonction d’orchestrateur. Par exemple, considérez la fonction d’orchestrateur suivante :

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Les données de journal obtenues se présentent comme suit :

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> N’oubliez pas que même si les journaux annoncent appeler les fonctions F1, F2 et F3, ces fonctions sont uniquement *réellement* appelées la première fois qu’elles sont rencontrées. Les prochains appels qui se produisent lors de la réexécution sont ignorés et les sorties sont réexécutées à la logique d’orchestrateur.

Si vous souhaitez uniquement consigner une non réexécution, vous pouvez écrire une expression conditionnelle qui écrit dans le journal uniquement si `IsReplaying` est `false`. Considérez l’exemple ci-dessus, mais cette fois avec des vérifications de réexécution.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Avec cette modification, la sortie du journal est la suivante :

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>État personnalisé

L’état d’orchestration personnalisé vous permet de définir une valeur d’état personnalisée pour votre fonction d’orchestrateur. Cet état est fourni par le biais de l’API de requête d’état HTTP ou l’API `DurableOrchestrationClient.GetStatusAsync`. L’état d’une orchestration personnalisée permet de surveiller plus précisément les fonctions d’orchestrateur. Par exemple, le code de fonction d’orchestrateur peut inclure les appels `DurableOrchestrationContext.SetCustomStatus` afin de mettre à jour la progression d’une opération de longue durée. Un client, comme une page web ou un autre système externe, peut ensuite interroger régulièrement les API de requête d’état HTTP pour en savoir plus sur l’état d’avancement. Voici un exemple de syntaxe utilisant `DurableOrchestrationContext.SetCustomStatus` :

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Pendant l’exécution de l’orchestration, les clients externes peuvent récupérer cet état personnalisé :

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Les clients obtiennent la réponse suivante :

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> La charge utile de l’état personnalisé est limitée à 16 Ko de texte JSON UTF-16, car elle doit pouvoir tenir dans une colonne de Stockage Table Azure. Vous pouvez utiliser un stockage externe si vous avez besoin d’une charge utile plus importante.

## <a name="debugging"></a>Débogage

Azure Functions prend directement en charge un code de fonction de débogage, et cette prise en charge s’applique également à Fonctions durables, que ce soit avec une exécution dans Azure ou localement. Toutefois, vous devez tenir compte de certains comportements lors du débogage :

* **Réexécution** : Les fonctions Orchestrator sont régulièrement réexécutées lors de la réception de nouvelles entrées. Cela signifie qu’une seule exécution *logique* d’une fonction d’orchestrateur peut atteindre le même point d’arrêt plusieurs fois, en particulier si elle est définie très tôt dans le code de la fonction.
* **Await** : Chaque fois qu’un événement `await` est rencontré, le contrôle repasse au répartiteur Durable Task Framework. Si c’est la première fois qu’un événement `await` particulier est détecté, la tâche associée n’est *jamais* reprise. Puisque la tâche ne reprend jamais, vous ne pouvez pas *parcourir* l’événement await (F10 dans Visual Studio). Parcourez uniquement des travaux lorsqu’une tâche est réexécutée.
* **Délais d’expiration des messages** : Durable Functions utilise en interne des files d’attente de messages pour gérer l’exécution des fonctions d’orchestrateur et d’activité. Dans un environnement à plusieurs machines virtuelles, si vous arrêtez le débogage pendant de longues périodes, une autre machine virtuelle risque de récupérer le message, ce qui entraîne une double exécution. Ce comportement s’applique également aux fonctions déclenchées par une file d’attente standard, mais il est important de le souligner car les files d’attente constituent un détail d’implémentation.

> [!TIP]
> Lors de la définition de points d’arrêt, si vous voulez uniquement arrêter en cas de non réexécution, vous pouvez définir un point d’arrêt conditionnel qui s’arrête uniquement si `IsReplaying` est `false`.

## <a name="storage"></a>Stockage

Par défaut, Durable Functions stocke l’état dans Stockage Azure. Cela signifie que vous pouvez examiner l’état de vos orchestrations à l’aide d’outils tels que l’[Explorateur Stockage Azure Microsoft](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Capture d’écran de l’Explorateur de stockage Azure](./media/durable-functions-diagnostics/storage-explorer.png)

Cet outil est utile pour le débogage car il vous permet de visualiser exactement l’état d’une orchestration. Vous pouvez également examiner les messages de files d’attente pour identifier les tâches en attente (ou bloquées dans certains cas).

> [!WARNING]
> Même s’il est pratique d’afficher l’historique d’exécution dans le stockage de table, évitez de créer une dépendance sur cette table. Elle peut changer à mesure que l’extension Fonctions durables évolue.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment utiliser les minuteurs durables](durable-functions-timers.md)
