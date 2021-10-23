---
title: Démarrage rapide - Soumettre un travail à mettre en file d’attente et à router
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez apprendre à créer un client de routeur de travaux, une stratégie de distribution, une file d’attente et un travail au sein de votre ressource Azure Communication Services.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/18/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ffad9a33c932300dbf52989536663215f8abb8f9
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164752"
---
# <a name="quickstart-submit-a-job-for-queuing-and-routing"></a>Démarrage rapide : Soumettre un travail à mettre en file d’attente et à router

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Découvrez comment bien démarrer avec le routeur de travaux Azure Communication Services en configurant votre client. Vous pourrez ensuite configurer les fonctionnalités de base telles que les files d’attente, les stratégies, les Workers et les travaux. Pour en savoir plus sur les concepts du routeur de travaux, consultez la [documentation conceptuelle du routeur de travaux](../../concepts/router/concepts.md)

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `JobRouterQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o JobRouterQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd JobRouterQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Durant la préversion privée, téléchargez le kit SDK à partir de [GitHub](https://github.com/Azure/communication-preview/releases/tag/communication-job-router-net-v1.0.0-alpha.20211012.1).

> [!NOTE]
> Vous devez être membre du groupe lié à la préversion privée pour télécharger les kits SDK.

Ajoutez les directives `using` suivantes en haut de **Program.cs** pour inclure les espaces de noms JobRouter.

```csharp
using Azure.Communication.JobRouter;
```

Mettez à jour la signature de la fonction `Main` pour la rendre `async` et retourner `Task`.

```csharp
static async Task Main(string[] args)
{
  ...
}
```

## <a name="authenticate-the-job-router-client"></a>Authentifier le client du routeur de travaux

Les clients du routeur de travaux peuvent être authentifiés à l’aide de la chaîne de connexion acquise à partir d’une ressource Azure Communication Services dans le portail Azure.

```csharp
// Get a connection string to our Azure Communication Services resource.
var connectionString = "your_connection_string";
var client = new RouterClient(connectionString);
```

## <a name="create-a-distribution-policy"></a>Créer une stratégie de distribution

Le routeur de travaux utilise une stratégie de distribution pour déterminer la façon dont les Workers doivent être notifiés concernant les travaux disponibles et la durée de vie des notifications. On appelle celles-ci des **offres**. Créez la stratégie en spécifiant l’**ID**, un **nom**, un **offerTTL** et un **mode** de distribution.

```csharp
var distributionPolicy = await client.SetDistributionPolicyAsync(
    id: "Longest_Idle_45s_Min1Max10",
    name: "Longest Idle matching with a 45s offer expiration; min 1, max 10 offers",
    offerTTL: TimeSpan.FromSeconds(45),
    mode: new LongestIdleMode(
        minConcurrentOffers: 1,
        maxConcurrentOffers: 10)
);
```

## <a name="create-a-queue"></a>Créer une file d’attente

Les travaux sont organisés sous forme de file d’attente logique. Créez la file d’attente en spécifiant un **ID**, un **nom**, puis indiquez l’ID de l’objet de **stratégie de distribution** que vous avez créé ci-dessus.

```csharp
var queue = await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: distributionPolicy.Value.Id
);
```

## <a name="submit-a-job"></a>Soumettre un travail
Le moyen le plus rapide de démarrer consiste à spécifier l’ID de la file d’attente, la priorité et les impératifs du Worker au moment de la soumission d’un travail. Dans l’exemple ci-dessous, un travail est soumis directement à la **file d’attente XBOX**, où les Workers de cette file d’attente recherchent une étiquette `Location` correspondant au nom `Edmonton`.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsChatChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelector: new List<LabelSelector>
    {
        new (
            key: "Location", 
            @operator: LabelOperator.Equal, 
            value: "Edmonton")
    });
```

## <a name="register-a-worker"></a>Inscrire un Worker
Inscrivez un Worker en référençant l’ID de file d’attente créé avec une valeur de **capacité**, des **étiquettes** et une **configuration de canal** pour garantir que `EdmontonWorker` est affecté à « XBOX_Queue ».

```csharp
var edmontonWorker = await client.RegisterWorkerAsync(
    id: "EdmontonWorker",
    queueIds: new []{ queue.Value.Id },
    totalCapacity: 100,
    labels: new LabelCollection()
    {
        {"Location", "Edmonton"}
    },
    channelConfigurations: new List<ChannelConfiguration>
    {
        new (
            channelId: ManagedChannels.AcsVoiceChannel,
            capacityCostPerJob: 100)
    }
);
```

## <a name="query-the-worker-to-observe-the-job-offer"></a>Interroger le Worker pour observer l’offre de travail
Utilisez la connexion cliente du routeur de travaux pour interroger le Worker et comparer l’ID du travail à l’ID 

```csharp
    // wait 500ms for the Job Router to offer the Job to the Worker
    Task.Delay(500).Wait();

    var result = await client.GetWorkerAsync(edmontonWorker.Value.Id);

    Console.WriteLine(
        $"Job ID: {job.Value.Id} offered to {edmontonWorker.Value.Id} " +
        $"should match Job ID attached to worker: {result.}");
```

Exécutez l’application à l’aide de `dotnet run` et observez les résultats.

```console
dotnet run

Job 6b83c5ad-5a92-4aa8-b986-3989c791be91 offered to EdmontonWorker should match Job ID from offer attached to worker: 6b83c5ad-5a92-4aa8-b986-3989c791be91
```

> [!NOTE]
> L’exécution de l’application à plusieurs reprises entraîne le placement d’un nouveau travail en file d’attente à chaque fois. Ainsi, le Worker peut se voir proposer un autre travail que celui créé quand vous avez exécuté le code ci-dessus. Dans la mesure où cela peut fausser votre requête, supprimez à chaque fois les travaux de la file d’attente. Consultez la documentation du kit SDK pour la gestion d’une file d’attente ou d’un travail.