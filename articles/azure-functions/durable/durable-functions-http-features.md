---
title: Fonctionnalités HTTP dans Durable Functions - Azure Functions
description: Découvrez les fonctionnalités HTTP intégrées dans l’extension Durable Functions pour Azure Functions.
author: cgillum
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: b909918ff4f9abc1dd64d4c7e5ccb35954b233f7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935546"
---
# <a name="http-features"></a>Fonctionnalités HTTP

Durable Functions propose plusieurs fonctionnalités pour faciliter l’incorporation d’orchestrations et d’entités durables dans les workflows HTTP. Cet article décrit en détail certaines de ces fonctionnalités.

## <a name="exposing-http-apis"></a>Exposition des API HTTP

Les orchestrations et les entités peuvent être appelées et gérées à l’aide de requêtes HTTP. L’extension Durable Functions expose les API HTTP intégrées et fournit également des API pour interagir avec les orchestrations et les entités au sein des fonctions déclenchées via HTTP.

### <a name="built-in-http-apis"></a>API HTTP intégrées

L’extension Durable Functions ajoute automatiquement un ensemble d’API HTTP à l’hôte Azure Functions. Ces API vous permettent d’interagir avec les orchestrations et les entités, et de les gérer, sans écrire de code.

Les API HTTP intégrées suivantes sont prises en charge.

* [Démarrer une nouvelle orchestration](durable-functions-http-api.md#start-orchestration)
* [Interroger l’instance d’orchestration](durable-functions-http-api.md#get-instance-status)
* [Mettre fin à l’instance d’orchestration](durable-functions-http-api.md#terminate-instance)
* [Envoyer un événement externe à une orchestration](durable-functions-http-api.md#raise-event)
* [Vider l’historique d’orchestration](durable-functions-http-api.md#purge-single-instance-history)
* [Envoyer un événement d’opération à une entité](durable-functions-http-api.md#signal-entity)
* [Interroger l’état d’une entité](durable-functions-http-api.md#query-entity)

Consultez l’article sur les [API HTTP](durable-functions-http-api.md) pour obtenir une description complète de toutes les API HTTP intégrées exposées par l’extension Durable Functions.

### <a name="http-api-url-discovery"></a>Découverte de l’URL de l’API HTTP

La [liaison du client d’orchestration](durable-functions-bindings.md#orchestration-client) expose les API qui peuvent être utilisées pour générer des charges utiles de réponse HTTP. Par exemple, elle peut créer une réponse contenant des liens vers des API de gestion pour une instance d’orchestration spécifique. Voici un exemple de fonction de déclencheur HTTP qui montre comment utiliser cette API pour une nouvelle instance d’orchestration :

#### <a name="precompiled-c"></a>C# précompilé

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

#### <a name="c-script"></a>Script C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

#### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x uniquement)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

#### <a name="functionjson"></a>Function.json

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Le démarrage d’une fonction d’orchestrateur à l’aide des fonctions de déclencheur HTTP présentées ci-dessus peut être effectué à l’aide d’un client HTTP quelconque. La commande curl suivante démarre une fonction d’orchestrateur nommée `DoWork`.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

Voici un exemple de réponse pour une orchestration dotée de l’ID `abc123` (certains détails ont été supprimés pour plus de clarté) :

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Chacun des champs `~Uri` de l’exemple précédent correspond aux API HTTP intégrées. Ces API peuvent être utilisées pour gérer l’instance d’orchestration cible.

> [!NOTE]
> Le format des URL « webhook » peut différer selon la version de l’hôte Azure Functions que vous exécutez. L’exemple ci-dessus utilise l’hôte Azure Functions 2.0.

Pour obtenir une description de toutes les API HTTP intégrées, consultez la documentation [Référence sur l’API HTTP](durable-functions-http-api.md).

### <a name="async-operation-tracking"></a>Suivi de l’opération asynchrone

La réponse HTTP mentionnée précédemment est conçue pour faciliter l’implémentation d’API asynchrones HTTP longues avec Durable Functions. Ce modèle est parfois appelé *modèle d’interrogation du consommateur*. Le flux client/serveur fonctionne comme suit :

1. Le client émet une requête HTTP pour démarrer un processus à long terme, par exemple une fonction d’orchestrateur.
2. Le déclencheur HTTP cible renvoie une réponse HTTP 202 dont l’en-tête `Location` a la valeur `statusQueryGetUri`.
3. Le client interroge l’URL dans l’en-tête `Location`. Il continue de voir les réponses HTTP 202 avec un en-tête `Location`.
4. Lorsque l’instance se termine (ou échoue), le point de terminaison dans l’en-tête `Location` renvoie HTTP 200.

Ce protocole permet de coordonner les processus à long terme avec des clients ou des services externes qui prennent en charge l’interrogation d’un point de terminaison HTTP et le suivi de l’en-tête `Location`. Les implémentations cliente et serveur de ce modèle sont intégrées dans les API HTTP de Durable Functions.

> [!NOTE]
> Par défaut, toutes les actions basées sur HTTP fournies par [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) prennent en charge le modèle d’opération asynchrone standard. Cette fonctionnalité permet d’incorporer une fonction durable de longue durée dans un flux de travail Logic Apps. Plus d’informations sur la prise en charge des modèles HTTP asynchrones par Logic Apps, consultez la [documentation sur les actions de flux de travail et les déclencheurs Azure Logic Apps](../../logic-apps/logic-apps-workflow-actions-triggers.md).

> [!NOTE]
> L’interaction avec les orchestrations peut être effectuée à partir de n’importe quel type de fonction, pas seulement des fonctions déclenchées par HTTP.

Pour plus d’informations sur la façon de gérer les orchestrations et les entités à l’aide des API clientes, consultez l’article [Gestion d’instance](durable-functions-instance-management.md).

## <a name="consuming-http-apis"></a>Utilisation des API HTTP

Les fonctions d’orchestrateur ne sont pas autorisées à effectuer directement des opérations d’E/S, comme cela est décrit dans les [contraintes de code des fonctions d’orchestrateur](durable-functions-code-constraints.md). Au lieu de cela, les fonctions d’orchestrateur appellent généralement des [fonctions d’activité](durable-functions-types-features-overview.md#activity-functions) qui effectuent les opérations d’E/S.

Depuis Durable Functions 2.0, les orchestrations sont en mesure de consommer en mode natif les API HTTP à l’aide de la [liaison de déclencheur d’orchestration](durable-functions-bindings.md#orchestration-trigger).

> [!NOTE]
> La capacité à appeler des points de terminaison HTTP directement à partir de fonctions d’orchestrateur n’est pas encore disponible dans JavaScript.

L’exemple de code suivant montre une fonction d’orchestrateur C# qui exécute une requête HTTP sortante à l’aide de l’API .NET `CallHttpAsync` :

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

L’action « call HTTP » vous permet d’effectuer les opérations suivantes dans vos fonctions d’orchestrateur :

* Appeler les API HTTP directement à partir des fonctions d’orchestration (avec certaines limitations mentionnées ultérieurement).
* Prendre en charge automatiquement les modèles d’interrogation d’état HTTP 202 côté client.
* Utiliser les [identités managées Azure](../../active-directory/managed-identities-azure-resources/overview.md) pour effectuer des appels HTTP autorisés à d’autres points de terminaison Azure.

La capacité à consommer les API HTTP directement à partir des fonctions d’orchestrateur est conçue à titre de commodité pour un certain ensemble de scénarios courants. Vous pouvez implémenter toutes ces fonctionnalités vous-même à l’aide des fonctions d’activité. Dans de nombreux cas, les fonctions d’activité peuvent vous offrir plus de souplesse.

### <a name="http-202-handling"></a>Gestion des cas HTTP 202

L’API « call HTTP » peut implémenter automatiquement le côté client du *modèle d’interrogation de consommateur*. Si une API appelée renvoie une réponse HTTP 202 avec un en-tête `Location`, la fonction d’orchestrateur interroge automatiquement la ressource `Location` jusqu’à ce qu’une réponse autre que 202 soit renvoyée. La réponse autre que 202 correspond à la réponse renvoyée au code de la fonction d’orchestrateur.

> [!NOTE]
> Les fonctions d’orchestrateur prennent également en charge en mode natif le *modèle d’interrogation de consommateur* côté serveur, tel qu’il est décrit dans [Suivi de l’opération asynchrone](#async-operation-tracking). Cela signifie que les orchestrations au sein d’une application de fonction peuvent facilement coordonner les fonctions d’orchestrateur dans d’autres applications de fonction. Cela est similaire au concept de [sous-orchestration](durable-functions-sub-orchestrations.md), mais avec la prise en charge des communications entre applications. Cela s’avère particulièrement utile pour le développement d’applications de type microservice.

### <a name="managed-identities"></a>Identités managées

Durable Functions prend en charge en mode natif l’appel des API qui acceptent des jetons Azure AD pour l’autorisation. Cette prise en charge utilise des [identités managées Azure](../../active-directory/managed-identities-azure-resources/overview.md) pour acquérir ces jetons.

Le code suivant est un exemple de fonction d’orchestrateur .NET qui effectue des appels authentifiés pour redémarrer une machine virtuelle à l’aide de l’[API REST Machines virtuelles](https://docs.microsoft.com/rest/api/compute/virtualmachines) d’Azure Resource Manager.

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

Dans l’exemple précédent, le paramètre `tokenSource` est configuré pour acquérir des jetons Azure AD pour [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) (identifiés par l’URI de ressource `https://management.core.windows.net`). Cet exemple part du principe que l’application de fonction actuelle s’exécute localement ou a été déployée en tant qu’application Azure Functions avec une identité managée. L’identité locale ou l’identité managée est supposée avoir l’autorisation de gérer des machines virtuelles dans le groupe de ressources spécifié, `myRG`.

Lors de l’exécution, la source de jeton configurée retourne automatiquement un jeton d’accès OAuth 2.0 et l’ajoute en tant que jeton du porteur à l’en-tête `Authorization` de la requête sortante. Ce modèle est une amélioration par rapport à l’ajout manuel d’en-têtes d’autorisation aux requêtes HTTP, car :

* L’actualisation des jetons est gérée automatiquement. Vous n’avez pas à vous soucier des jetons expirés.
* Les jetons ne sont jamais stockés dans l’état d’orchestration durable.
* Vous n’êtes pas obligé d’écrire du code pour gérer l’acquisition des jetons.

Vous trouverez un exemple plus complet dans les [exemples « RestartVMs » C# précompilés](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs).

Les identités managées ne sont pas limitées à la gestion des ressources Azure. Les identités managées peuvent être utilisées pour accéder à n’importe quelle API qui accepte les jetons de porteur Azure AD, y compris les services Azure internes ou les applications web tierces. L’application web tierce peut même être une autre application de fonction. Pour obtenir la liste des services Azure internes qui prennent en charge l’authentification avec Azure AD, consultez [Services Azure qui prennent en charge l’authentification Azure AD](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

### <a name="limitations"></a>Limites

La prise en charge intégrée de l’appel des API HTTP est une fonctionnalité de commodité qui ne convient pas pour tous les scénarios. Les requêtes HTTP envoyées par les fonctions d’orchestrateur et leurs réponses sont sérialisées et conservées en tant que messages de file d’attente. Ce comportement de mise en file d’attente garantit [la fiabilité et la sécurité des appels HTTP pour la relecture d’orchestration](durable-functions-orchestrations.md#reliability). Toutefois, ce comportement de mise en file d’attente signifie également que :

* Chaque requête HTTP implique une latence supplémentaire par rapport à un client HTTP natif.
* Les messages de demande ou de réponse volumineux qui ne peuvent pas tenir dans un message de file d’attente peuvent nuire considérablement aux performances de l’orchestration. La dégradation potentielle des performances est due à la surcharge du déchargement des charges utiles des messages dans le stockage d’objets blob.
* Les charges utiles de streaming, segmentées et binaires ne sont pas prises en charge.
* La possibilité de personnaliser le comportement du client HTTP est limitée.

Si l’une de ces limitations peut avoir un impact sur votre cas d’utilisation, envisagez plutôt d’utiliser des fonctions d’activité et des bibliothèques clientes HTTP spécifiques à un langage pour effectuer des appels HTTP sortants.

> [!NOTE]
> Si vous êtes un développeur .NET, vous vous demandez peut-être pourquoi cette fonctionnalité utilise les types `DurableHttpRequest` et `DurableHttpResponse` au lieu des types .NET intégrés `HttpRequestMessage` et `HttpResponseMessage`. Ce choix de conception est intentionnel. La raison principale est que les types personnalisés permettent de s’assurer que les utilisateurs ne font pas de suppositions incorrectes sur les comportements pris en charge du client HTTP interne. Les types spécifiques aux fonctions durables permettent également de simplifier la conception des API et de mettre plus facilement en lumière des fonctionnalités spéciales, telles que l’[intégration des identités managées](#managed-identities) et le [modèle d’interrogation de consommateur](#http-202-handling).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les entités durables](durable-functions-entities.md)