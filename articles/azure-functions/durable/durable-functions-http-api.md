---
title: API HTTP dans Fonctions durables - Azure
description: Découvrez comment implémenter des API HTTP dans l’extension Fonctions durables d’Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 2f0b01601dfb28b2b6b8ee8ca53398ec3dccb803
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787283"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>API HTTP dans Fonctions durables (Azure Functions)

L’extension Tâche durable expose un ensemble d’API HTTP qui peut être utilisé pour effectuer les tâches suivantes :

* Récupérer l'état d'une instance d’orchestration.
* Envoyer un événement à une instance d’orchestration en attente.
* Mettre fin à une instance d’orchestration en cours d’exécution.

Chacune de ces API HTTP est une opération webhook gérée directement par l’extension Tâche durable. Elles ne sont pas spécifiques à une fonction dans l’application de fonction.

> [!NOTE]
> Ces opérations peuvent également être appelées directement à l’aide des API de gestion d’instance sur la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Pour plus d’informations, consultez [Gestion d’instance](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Découverte de l’URL de l’API HTTP

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expose une API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) qui peut être utilisée pour générer une charge utile de réponse HTTP contenant des liens vers toutes les opérations prises en charge. Voici un exemple de fonction de déclencheur HTTP qui montre comment utiliser cette API :

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (fonctions 2.x uniquement)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Ces exemples de fonctions produisent les données de réponse JSON suivantes. Le type de données de tous les champs est `string`.

| Champ                   |Description                           |
|-------------------------|--------------------------------------|
| **`id`**                |L’ID de l’instance d’orchestration. |
| **`statusQueryGetUri`** |L’URL de l’état de l’instance d’orchestration. |
| **`sendEventPostUri`**  |L’URL « Raise event » de l’instance d’orchestration. |
| **`terminatePostUri`**  |L’URL « d’arrêt » de l’instance d’orchestration. |
| **`rewindPostUri`**     |URL de «rembobinage» de l’instance d’orchestration. |

Voici un exemple de réponse :

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> Le format des URL « webhook » peut différer selon la version de l’hôte Azure Functions que vous exécutez. L’exemple ci-dessus correspond à l’hôte Azure Functions 2.x.

## <a name="async-operation-tracking"></a>Suivi de l’opération asynchrone

La réponse HTTP mentionnée précédemment est conçue pour faciliter l’implémentation d’API asynchrones HTTP longues avec Durable Functions. Cette opération est parfois appelée *modèle d’interrogation du consommateur*. Le flux client/serveur fonctionne comme suit :

1. Le client émet une requête HTTP pour démarrer un processus à long terme, par exemple une fonction d’orchestrateur.
2. Le déclencheur HTTP cible renvoie une réponse HTTP 202 dont l’en-tête `Location` a la valeur `statusQueryGetUri`.
3. Le client interroge l’URL dans l’en-tête `Location`. Il continue de voir les réponses HTTP 202 avec un en-tête `Location`.
4. Lorsque l’instance se termine (ou échoue), le point de terminaison dans l’en-tête `Location` renvoie HTTP 200.

Ce protocole permet de coordonner les processus à long terme avec des clients ou des services externes qui prennent en charge l’interrogation d’un point de terminaison HTTP et le suivi de l’en-tête `Location`. Les éléments fondamentaux sont déjà intégrés aux API HTTP de Fonctions durables.

> [!NOTE]
> Par défaut, toutes les actions basées sur HTTP fournies par [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) prennent en charge le modèle d’opération asynchrone standard. Cette fonctionnalité permet d’incorporer une fonction durable de longue durée dans un flux de travail Logic Apps. Plus d’informations sur la prise en charge des modèles HTTP asynchrones par Logic Apps, consultez la [documentation sur les actions de flux de travail et les déclencheurs Azure Logic Apps](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Référence sur l'API HTTP

Toutes les API HTTP implémentées par l’extension utilisent les paramètres suivants. Le type de données de tous les paramètres est `string`.

| Paramètre        | Type de paramètre  | Description |
|------------------|-----------------|-------------|
| **`taskHub`**    | Chaîne de requête    | Le nom du [hub de tâches](durable-functions-task-hubs.md). S’il n’est pas spécifié, le nom de hub de tâches de l’application de fonction en cours est supposé. |
| **`connection`** | Chaîne de requête    | Le **nom** de la chaîne de connexion du compte de stockage. Si elle n’est pas spécifiée, la chaîne de connexion par défaut pour l’application de la fonction est supposée. |
| **`systemKey`**  | Chaîne de requête    | La clé d’autorisation requise pour appeler l’API. |

`systemKey` est une clé d’autorisation automatiquement générée par l’hôte Azure Functions. Elle accorde l’accès aux API de l’extension Tâche durable et peut être gérée de la même façon que les [autres clés d’autorisation](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). La façon la plus simple pour découvrir la valeur `systemKey` consiste à utiliser l’API `CreateCheckStatusResponse` mentionnée précédemment.

Les quelques sections suivantes présentent les API HTTP spécifiques prises en charge par l’extension et fournissent des exemples d’utilisation.

### <a name="get-instance-status"></a>Obtenir l’état de l’instance

Obtient l'état d'une instance d’orchestration spécifiée.

#### <a name="request"></a>Requête

Pour la version 1.x du runtime Functions, la demande est mise en forme comme suit (plusieurs lignes sont affichées par souci de clarté) :

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

En version 2.x du runtime Functions, le format d’URL comporte les mêmes paramètres, mais présente un préfixe légèrement différent :

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Les paramètres de requête pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que les paramètres uniques suivants :

| Champ                   | Type de paramètre  | Description |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | L’ID de l’instance d’orchestration. |
| **`showInput`**         | Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `false`, la fonction n’est pas incluse dans la charge utile de la réponse.|
| **`showHistory`**       | Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `true`, l’historique d’exécution de l’orchestration est inclus dans la charge utile de réponse.|
| **`showHistoryOutput`** | Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `true`, les sorties de la fonction sont incluses dans l’historique d’exécution de l’orchestration.|
| **`createdTimeFrom`**   | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances retournées qui ont été créées pendant ou après l’horodatage ISO8601 donné.|
| **`createdTimeTo`**     | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances retournées qui ont été créées pendant ou avant l’horodatage ISO8601 donné.|
| **`runtimeStatus`**     | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances retournées selon leur état d’exécution. Pour obtenir la liste des valeurs d’état d’exécution possibles, consultez la rubrique [Interrogation des instances](durable-functions-instance-management.md). |

#### <a name="response"></a>response

Plusieurs valeurs de code d’état possibles peuvent être retournées.

* **HTTP 200 (OK)** : L’instance spécifiée indique un état terminé.
* **HTTP 202 (acceptée)** : L’instance spécifiée est en cours d’exécution.
* **HTTP 400 (requête incorrecte)** : L’instance spécifiée a échoué ou a été arrêtée.
* **HTTP 404 (introuvable)** : L’instance spécifiée n’existe pas ou n’a pas démarré.
* **HTTP 500 (erreur interne du serveur)** : L’instance spécifiée a échoué avec une exception non prise en charge.

La charge utile de réponse pour les cas **HTTP 200** et **HTTP 202** est un objet JSON avec les champs suivants :

| Champ                 | Type de données | Description |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | chaîne    | L’état d’exécution de l’instance. Les valeurs sont *Running*, *Pending*, *Failed*, *Canceled*, *Terminated*, *Completed*. |
| **`input`**           | JSON      | Les données JSON utilisées pour initialiser l’instance. Ce champ est `null` si le paramètre de chaîne de requête `showInput` est défini sur `false`.|
| **`customStatus`**    | JSON      | Données JSON utilisées pour l’état d’orchestration personnalisé. Ce champ est `null` s’il n’est pas défini. |
| **`output`**          | JSON      | La sortie JSON de l’instance. Ce champ est `null` si l’instance n’est pas dans un état terminé. |
| **`createdTime`**     | chaîne    | Heure à laquelle l’instance a été créée. Utilise la notation étendue ISO 8601. |
| **`lastUpdatedTime`** | chaîne    | Heure du dernier état persistant de l’instance. Utilise la notation étendue ISO 8601. |
| **`historyEvents`**   | JSON      | Tableau JSON contenant l’historique d’exécution de l’orchestration. Ce champ est `null`, sauf si le paramètre de chaîne de requête `showHistory` a la valeur `true`. |

Voici un exemple de charge utile de réponse incluant l’historique et les sorties de l’activité d’exécution d’orchestration (mis en forme pour une meilleure lisibilité) :

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

La réponse **HTTP 202** inclut également un en-tête de réponse **Location** qui fait référence à la même URL que le champ `statusQueryGetUri` mentionné précédemment.

### <a name="get-all-instances-status"></a>Obtenir l’état de toutes les instances

Vous pouvez également interroger l’état de toutes les instances en supprimant l’élément `instanceId` de la demande « Obtenir l’état de l’instance ». Dans le cas présent, les paramètres de base sont les mêmes que ceux de « Obtenir l’état de l’instance ». Les paramètres de chaîne de requête dédiés au filtrage sont également pris en charge.

Une chose à retenir est que `connection` et `code` sont facultatifs. Si vous disposez de l’authentification anonyme sur la fonction, le paramètre code n’est nécessaire.
Si vous ne souhaitez pas utiliser une autre chaîne de connexion de stockage que celle définie dans le paramètre d’application AzureWebJobsStorage, vous pouvez ignorer en toute sécurité le paramètre de chaîne de requête de connexion.

#### <a name="request"></a>Requête

Pour la version 1.x du runtime Functions, la demande est mise en forme comme suit (plusieurs lignes sont affichées par souci de clarté) :

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

En version 2.x du runtime Functions, le format d’URL comporte les mêmes paramètres, mais présente un préfixe légèrement différent :

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Les paramètres de requête pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que les paramètres uniques suivants :

| Champ                   | Type de paramètre  | Description |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | L’ID de l’instance d’orchestration. |
| **`showInput`**         | Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `false`, la fonction n’est pas incluse dans la charge utile de la réponse.|
| **`showHistory`**       | Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `true`, l’historique d’exécution de l’orchestration est inclus dans la charge utile de réponse.|
| **`showHistoryOutput`** | Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `true`, les sorties de la fonction sont incluses dans l’historique d’exécution de l’orchestration.|
| **`createdTimeFrom`**   | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances retournées qui ont été créées pendant ou après l’horodatage ISO8601 donné.|
| **`createdTimeTo`**     | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances retournées qui ont été créées pendant ou avant l’horodatage ISO8601 donné.|
| **`runtimeStatus`**     | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances retournées selon leur état d’exécution. Pour obtenir la liste des valeurs d’état d’exécution possibles, consultez la rubrique [Interrogation des instances](durable-functions-instance-management.md). |
| **`top`**               | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, limite le nombre d’instances retournées par la requête. |

#### <a name="response"></a>response

Voici un exemple de charges utiles de réponse incluant l’état de l’orchestration (mises en forme pour une meilleure lisibilité) :

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Cette opération peut être très gourmande en E/S pour le stockage Azure s’il y a un grand nombre de lignes dans la table d’instances. Vous trouverez plus d’informations sur la table d’instances dans la documentation [Performances et mise à l’échelle dans Fonctions durables (Azure Functions)](durable-functions-perf-and-scale.md#instances-table).
>

Si vous obtenez plus de résultats, un jeton de continuation est retourné dans l’en-tête de réponse.  Le nom de l’en-tête est `x-ms-continuation-token`.

Si vous définissez la valeur du jeton de continuation dans l’en-tête de la demande suivante, vous pouvez obtenir la page de résultats suivante. Cet en-tête de demande est également nommé `x-ms-continuation-token`.

### <a name="purge-single-instance-history"></a>Vider l’historique d’une instance unique

Supprime l’historique et les artefacts associés d’une instance d’orchestration spécifiée.

#### <a name="request"></a>Requête

Pour la version 1.x du runtime Functions, la demande est mise en forme comme suit (plusieurs lignes sont affichées par souci de clarté) :

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

En version 2.x du runtime Functions, le format d’URL comporte les mêmes paramètres, mais présente un préfixe légèrement différent :

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Les paramètres de requête pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que les paramètres uniques suivants :

| Champ             | Type de paramètre  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | L’ID de l’instance d’orchestration. |

#### <a name="response"></a>response

Les valeurs de code d’état HTTP peuvent être également être retournées.

* **HTTP 200 (OK)** : L’historique d’instance a été vidé avec succès.
* **HTTP 404 (introuvable)** : L’instance spécifiée n’existe pas.

La charge utile de réponse pour le cas **HTTP 200** est un objet JSON présentant le champ suivant :

| Champ                  | Type de données | Description |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | integer   | Nombre d’instances supprimées. Dans le cas d’une instance unique, cette valeur doit toujours être `1`. |

Voici un exemple de charge utile de réponse (mis en forme pour une meilleure lisibilité) :

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>Vider l’historique de plusieurs instances

Vous pouvez également supprimer l’historique et les artefacts associés de plusieurs instances au sein d’un hub de tâches en supprimant `{instanceId}` de la demande « Vider l’historique d’une instance unique ». Pour vider l’historique de certaines instances spécifiques, utilisez les mêmes filtres que ceux décrits dans la demande « Obtenir l’état de toutes les instances ».

#### <a name="request"></a>Requête

Pour la version 1.x du runtime Functions, la demande est mise en forme comme suit (plusieurs lignes sont affichées par souci de clarté) :

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

En version 2.x du runtime Functions, le format d’URL comporte les mêmes paramètres, mais présente un préfixe légèrement différent :

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Les paramètres de requête pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que les paramètres uniques suivants :

| Champ                 | Type de paramètre  | Description |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Chaîne de requête    | Filtre la liste des instances vidées qui ont été créées pendant ou après l’horodatage ISO8601 donné.|
| **`createdTimeTo`**   | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances vidées qui ont été créées pendant ou avant l’horodatage ISO8601 donné.|
| **`runtimeStatus`**   | Chaîne de requête    | Paramètre facultatif. Lorsqu’il est spécifié, filtre la liste des instances vidées selon l’état de leur runtime. Pour obtenir la liste des valeurs d’état d’exécution possibles, consultez la rubrique [Interrogation des instances](durable-functions-instance-management.md). |

> [!NOTE]
> Cette opération peut être très gourmande en E/S pour le stockage Azure s’il y a un grand nombre de lignes dans les instances et/ou les tables d’historique. Vous trouverez plus d’informations sur ces tables dans la documentation [Performances et diminution de la taille des instances dans Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table).

#### <a name="response"></a>response

Les valeurs de code d’état HTTP peuvent être également être retournées.

* **HTTP 200 (OK)** : L’historique d’instance a été vidé avec succès.
* **HTTP 404 (introuvable)** : Aucune instance correspondant à l’expression de filtre n’a été trouvée.

La charge utile de réponse pour le cas **HTTP 200** est un objet JSON présentant le champ suivant :

| Champ                   | Type de données | Description |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | integer   | Nombre d’instances supprimées. |

Voici un exemple de charge utile de réponse (mis en forme pour une meilleure lisibilité) :

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>Raise event

Envoie un message de notification d’événement à une instance d’orchestration en cours d’exécution.

#### <a name="request"></a>Requête

Pour la version 1.x du runtime Functions, la demande est mise en forme comme suit (plusieurs lignes sont affichées par souci de clarté) :

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

En version 2.x du runtime Functions, le format d’URL comporte les mêmes paramètres, mais présente un préfixe légèrement différent :

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Les paramètres de requête pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que les paramètres uniques suivants :

| Champ             | Type de paramètre  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | L’ID de l’instance d’orchestration. |
| **`eventName`**   | URL             | Le nom de l’événement que l’instance d’orchestration cible attend. |
| **`{content}`**   | Contenu de la demande | La charge utile de l’événement au format JSON. |

#### <a name="response"></a>response

Plusieurs valeurs de code d’état possibles peuvent être retournées.

* **HTTP 202 (acceptée)** : L’événement déclenché a été accepté pour traitement.
* **HTTP 400 (requête incorrecte)** : Le contenu de la requête n’était pas de type `application/json` ou n’était pas un objet JSON valide.
* **HTTP 404 (introuvable)** : L’instance spécifiée est introuvable.
* **HTTP 410 (absente)** : L’instance spécifiée est terminée ou a échoué et ne peut traiter aucun événement déclenché.

Voici un exemple de requête qui envoie la chaîne JSON `"incr"` à une instance en attente d’un événement nommé **operation** :

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Les réponses pour cette API sont vides.

### <a name="terminate-instance"></a>Arrêter une instance

Arrête une instance d’orchestration en cours d’exécution.

#### <a name="request"></a>Requête

Pour la version 1.x du runtime Functions, la demande est mise en forme comme suit (plusieurs lignes sont affichées par souci de clarté) :

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

En version 2.x du runtime Functions, le format d’URL comporte les mêmes paramètres, mais présente un préfixe légèrement différent :

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Les paramètres de la demande pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que le paramètre unique suivant.

| Champ             | Type de paramètre  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | L’ID de l’instance d’orchestration. |
| **`reason`**      | Chaîne de requête    | facultatif. Motif d’arrêt de l’instance d’orchestration. |

#### <a name="response"></a>response

Plusieurs valeurs de code d’état possibles peuvent être retournées.

* **HTTP 202 (acceptée)** : La demande d’arrêt a été acceptée pour traitement.
* **HTTP 404 (introuvable)** : L’instance spécifiée est introuvable.
* **HTTP 410 (absente)** : L’instance spécifiée est terminée ou a échoué.

Voici un exemple de demande qui met fin à une instance en cours d’exécution et affiche la raison **buggy** (bogué) :

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Les réponses pour cette API sont vides.

## <a name="rewind-instance-preview"></a>Instance de rembobinage (préversion)

Restaure une instance d’orchestration ayant échoué dans un état en cours d’exécution au travers de la relecture des dernières opérations ayant échoué.

### <a name="request"></a>Requête

Pour la version 1.x du runtime Functions, la demande est mise en forme comme suit (plusieurs lignes sont affichées par souci de clarté) :

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

En version 2.x du runtime Functions, le format d’URL comporte les mêmes paramètres, mais présente un préfixe légèrement différent :

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Les paramètres de la demande pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que le paramètre unique suivant.

| Champ             | Type de paramètre  | Description |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | L’ID de l’instance d’orchestration. |
| **`reason`**      | Chaîne de requête    | facultatif. Motif de rembobinage de l’instance d’orchestration. |

### <a name="response"></a>response

Plusieurs valeurs de code d’état possibles peuvent être retournées.

* **HTTP 202 (acceptée)** : La demande de rembobinage a été acceptée pour traitement.
* **HTTP 404 (introuvable)** : L’instance spécifiée est introuvable.
* **HTTP 410 (absente)** : L’instance spécifiée est terminée.

Voici un exemple de requête qui rembobine une instance ayant échoué et affiche la raison **corrigé** :

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Les réponses pour cette API sont vides.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment gérer les erreurs](durable-functions-error-handling.md)
