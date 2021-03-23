---
title: Tests unitaires de l’extension Fonctions durables Azure
description: Découvrez comment effectuer des tests unitaires sur l’extension Fonctions durables.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 89b6419e95b3971b0d272490e19354f300204e1e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491042"
---
# <a name="durable-functions-unit-testing"></a>Tests unitaires de l’extension Fonctions durables

Le test unitaire est une partie importante des pratiques de développement de logiciels modernes. Les tests unitaires vérifient le comportement de la logique métier et offrent une protection contre l’introduction, dans le futur, de changements importants qui passent inaperçus. Étant donné que l’extension Fonctions durables peut aisément se complexifier, les tests unitaires permettent d’éviter les changements importants. Les sections suivantes expliquent comment effectuer des tests unitaires sur les trois types de fonctions : Client d’orchestration, orchestrateur et fonctions d’activité.

> [!NOTE]
> Cet article fournit des conseils concernant les tests unitaires pour les applications Durable Functions ciblant Durable Functions 2.x. Pour en savoir plus sur les différences entre les versions, consultez l’article [Versions de Durable Functions](durable-functions-versions.md).

## <a name="prerequisites"></a>Prérequis

Les exemples de cet article exigent de connaître les concepts et frameworks suivants :

* Effectuer des tests unitaires

* Fonctions durables

* [xUnit](https://github.com/xunit/xunit) : framework de test

* [moq](https://github.com/moq/moq4) : framework de simulation

## <a name="base-classes-for-mocking"></a>Classes de base pour la simulation

La simulation est prise en charge via l’interface suivante :

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationClient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.IDurableEntityClient) et [IDurableClient](/dotnet/api/microsoft.azure.webjobs.IDurableClient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationContext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.IDurableActivityContext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.IDurableEntityContext)

Ces interfaces peuvent être utilisées avec les différents déclencheurs et liaisons pris en charge par Durable Functions. Lors de l’exécution d’Azure Functions, le runtime Functions exécute le code de votre fonction avec une implémentation concrète de ces interfaces. Pour les tests unitaires, vous pouvez transmettre une version factice de ces interfaces pour tester votre logique métier.

## <a name="unit-testing-trigger-functions"></a>Fonctions de déclencheur de test unitaire

Dans cette section, le test unitaire valide la logique de la fonction de déclencheur HTTP suivante pour démarrer de nouvelles orchestrations.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

La tâche du test unitaire est de vérifier la valeur de l’en-tête `Retry-After` fourni dans la charge utile de réponse. Le test unitaire simule alors certaines des méthodes `IDurableClient` pour garantir un comportement prévisible.

Tout d’abord, nous utilisons un framework de simulation ([moq](https://github.com/moq/moq4) dans ce cas) pour simuler `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> Bien que vous puissiez simuler l’interface en implémentant directement l’interface en tant que classe, les infrastructures factices simplifient le processus de différentes façons. Par exemple, si une nouvelle méthode est ajoutée à l’interface dans les versions mineures, moq ne nécessite aucune modification de code, contrairement aux implémentations concrètes.

La méthode `StartNewAsync` est simulée pour retourner un ID d’instance connu.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

`CreateCheckStatusResponse` est ensuite simulé pour toujours retourner une réponse HTTP 200 vide.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
    .Returns(new HttpResponseMessage
    {
        StatusCode = HttpStatusCode.OK,
        Content = new StringContent(string.Empty),
        Headers =
        {
            RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
        }
    });
```

`ILogger` est également simulé :

```csharp
// Mock ILogger
var loggerMock = new Mock<ILogger>();
```  

À présent, la méthode `Run` est appelée à partir du test unitaire :

```csharp
// Call Orchestration trigger function
var result = await HttpStart.Run(
    new HttpRequestMessage()
    {
        Content = new StringContent("{}", Encoding.UTF8, "application/json"),
        RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
    },
    durableClientMock.Object,
    functionName,
    loggerMock.Object);
 ```

 La dernière étape consiste à comparer la sortie avec la valeur attendue :

```csharp
// Validate that output is not null
Assert.NotNull(result.Headers.RetryAfter);

// Validate output's Retry-After header value
Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Une fois toutes les étapes combinées, le test unitaire a le code suivant :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Fonctions d’orchestrateur de test unitaire

Les fonctions d’orchestrateur sont encore plus intéressantes pour le test unitaire, car elles ont généralement beaucoup plus de logique métier.

Dans cette section, les tests unitaires valident la sortie de la fonction d’orchestrateur `E1_HelloSequence` :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

Le code de test unitaire commence par créer un objet fictif :

```csharp
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
```

Les appels de méthode d’activité sont alors simulés :

```csharp
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

Le test unitaire appelle ensuite la méthode`HelloSequence.Run` :

```csharp
var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

Enfin, la sortie est validée :

```csharp
Assert.Equal(3, result.Count);
Assert.Equal("Hello Tokyo!", result[0]);
Assert.Equal("Hello Seattle!", result[1]);
Assert.Equal("Hello London!", result[2]);
```

Une fois toutes les étapes combinées, le test unitaire a le code suivant :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Fonctions d’activité de test unitaire

Les fonctions d’activité peuvent faire l’objet d’un test unitaire de la même façon que les fonctions non durables.

Dans cette section, le test unitaire valide le comportement de la fonction d’activité `E1_SayHello` :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

De son côté, le test unitaire vérifie le format de la sortie. Les tests unitaires peuvent utiliser les types de paramètre directement ou simuler la classe `IDurableActivityContext` :

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [En savoir plus sur moq](https://github.com/Moq/moq4/wiki/Quickstart)
