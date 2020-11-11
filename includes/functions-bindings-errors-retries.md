---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284407"
---
Les erreurs signalées dans Azure Functions peuvent avoir l’une des origines suivantes :

- Utilisation des [déclencheurs et liaisons intégrés](..\articles\azure-functions\functions-triggers-bindings.md) Azure Functions
- Appels aux API des services Azure sous-jacents
- Appels aux points de terminaison REST
- Appels aux bibliothèques clientes, aux packages ou aux API tierces

Il est important de suivre les bonnes pratiques de gestion des erreurs afin d’éviter de perdre des données ou de manquer des messages. Les pratiques recommandées concernant la gestion des erreurs impliquent les actions suivantes :

- [Activer Application Insights](../articles/azure-functions/functions-monitoring.md)
- [Utiliser la gestion structurée des erreurs](#use-structured-error-handling)
- [Concevoir en ayant en vue l’idempotence](../articles/azure-functions/functions-idempotent.md)
- [Implémenter des stratégies relatives aux nouvelles tentatives](#retry-policies) (si nécessaire)

### <a name="use-structured-error-handling"></a>Utiliser la gestion structurée des erreurs

La capture et la journalisation des erreurs sont essentielles pour superviser l’intégrité de votre application. Le niveau le plus élevé de tout code de fonction doit inclure un bloc try/catch. Dans le bloc catch, vous pouvez capturer et journaliser les erreurs.

## <a name="retry-policies"></a>Stratégies de nouvelle tentative

Une stratégie de nouvelles tentatives peut être définie sur n’importe quelle fonction pour n’importe quel type de déclencheur dans votre application de fonction.  La stratégie de nouvelles tentatives réexécute une fonction jusqu’à ce que celle-ci aboutisse ou jusqu’à ce que le nombre maximal de nouvelles tentatives soit atteint.  Vous pouvez définir des stratégies de nouvelles tentatives pour toutes les fonctions d’une application ou pour des fonctions individuelles.  Par défaut, une application de fonction n’effectue pas de nouvelles tentatives pour les messages (à part les [déclencheurs spécifiques qui ont une stratégie de nouvelles tentatives sur la source du déclencheur](#trigger-specific-retry-support)).  Une stratégie de nouvelles tentatives est évaluée chaque fois qu’une exécution entraîne une exception non interceptée.  Il est recommandé d’intercepter toutes les exceptions dans votre code et de regénérer toute erreur susceptible d’entraîner une nouvelle tentative.  Les points de contrôle Event Hubs et Azure Cosmos DB ne sont pas écrits tant que la stratégie de nouvelles tentatives pour l’exécution n’est pas terminée, ce qui signifie que la progression sur cette partition est suspendue jusqu’à ce que le traitement du lot actuel soit terminé.

### <a name="retry-policy-options"></a>Options de stratégie de nouvelles tentatives

Les options suivantes permettent de définir une stratégie de nouvelles tentatives.

**Nombre maximal de nouvelles tentatives** correspond au nombre maximal de nouvelles tentatives d’exécution avant un éventuel échec. Une valeur de `-1` signifie qu’il faut effectuer ces nouvelles tentatives indéfiniment. Le nombre actuel de nouvelles tentatives est stocké dans la mémoire de l’instance. Il est possible qu’un échec d’instance se produise entre deux nouvelles tentatives.  Quand une instance échoue durant une stratégie de nouvelles tentatives, le nombre de nouvelles tentatives est perdu.  En cas d’échec d’instance, les déclencheurs comme Event Hubs, Azure Cosmos DB et Queue Storage peuvent reprendre le traitement et retenter le lot sur une nouvelle instance, le nombre de nouvelles tentatives étant réinitialisé à zéro.  D’autres déclencheurs, tels que ceux basés sur HTTP ou un minuteur, ne reprennent pas le traitement sur une nouvelle instance.  Cela signifie que le nombre maximal de nouvelles tentatives est régi par le principe du « meilleur effort ». Dans certains cas rares, le nombre maximal de nouvelles tentatives d’exécution peut être supérieur au maximum ou, pour des déclencheurs basés sur HTTP ou un minuteur, inférieur au maximum.

La **stratégie de nouvelles tentatives** contrôle le comportement des nouvelles tentatives.  Voici deux options de nouvelle tentative prises en charge :

| Option | Description|
|---|---|
|**`fixedDelay`**| Un laps de temps spécifié est autorisé à s’écouler entre chaque nouvelle tentative.|
| **`exponentialBackoff`**| La première nouvelle tentative attend le délai minimal. Lors des nouvelles tentatives suivantes, le laps de temps est ajouté de manière exponentielle à la durée initiale de chaque nouvelle tentative jusqu’à ce que le délai maximal soit atteint.  Le back-off exponentiel ajoute une faible randomisation aux délais pour échelonner les nouvelles tentatives dans les scénarios à débit élevé.|

#### <a name="app-level-configuration"></a>Configuration au niveau d’une l’application

Une stratégie de nouvelles tentatives peut être définie pour toutes les fonctions d’une application [à l’aide du fichier `host.json`](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Configuration au niveau d’une fonction

Vous pouvez définir une stratégie de nouvelles tentatives pour une fonction spécifique.  Une configuration spécifique à une fonction a priorité sur une configuration au niveau d’une application.

#### <a name="fixed-delay-retry"></a>Nouvelle tentative avec délai fixe

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Nouvelle tentative avec backoff exponentiel

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Voici la stratégie de nouvelles tentatives dans le fichier *function.json*  :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|Propriété function.json  |Propriété d'attribut | Description |
|---------|---------|---------| 
|stratégie|Non applicable|Obligatoire. Stratégie de nouvelle tentative à utiliser. Les valeurs valides sont `fixedDelay` ou `exponentialBackoff`.|
|maxRetryCount|Non applicable|Obligatoire. Nombre maximal de nouvelles tentatives autorisées par exécution de fonction. `-1` signifie qu’il faut effectuer ces nouvelles tentatives indéfiniment.|
|delayInterval|Non applicable|Délai utilisé entre chaque nouvelle tentative lors de l’utilisation d’une stratégie `fixedDelay`.|
|minimumInterval|Non applicable|Délai minimal de nouvelle tentative lors de l’utilisation de la stratégie `exponentialBackoff`.|
|maximumInterval|Non applicable|Délai maximal de nouvelle tentative lors de l’utilisation de la stratégie `exponentialBackoff`.| 

## <a name="trigger-specific-retry-support"></a>Prise en charge des nouvelles tentatives spécifiques aux déclencheurs

Certains déclencheurs fournissent de nouvelles tentatives au niveau de la source du déclencheur.  Ces nouvelles tentatives peuvent être utilisées en plus ou en remplacement de la stratégie de nouvelles tentatives de l’hôte d’application de fonction.  Si vous souhaitez un nombre fixe de nouvelles tentatives, utilisez la stratégie de nouvelles tentatives spécifique au déclencheur plutôt que la stratégie de nouvelles tentatives de l’hôte générique.  Les déclencheurs suivants prennent en charge les nouvelles tentatives au niveau de la source du déclencheur :

* [stockage d’objets blob Azure](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Stockage File d’attente Azure](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (file d’attente/rubrique)](../articles/azure-functions/functions-bindings-service-bus.md)

Par défaut, ces déclencheurs retentent les requêtes jusqu’à cinq fois. Après la cinquième tentative, le Stockage File d’attente Azure et le déclencheur Azure Service Bus écrivent un message dans une [file d’attente de messages incohérents](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).
