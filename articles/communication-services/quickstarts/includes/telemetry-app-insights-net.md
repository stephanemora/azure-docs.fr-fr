---
title: Fichier Include
description: Fichier include
services: azure-communication-services
author: peiliu
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 983ce28f736bd36255e65073b202ff6c5761b059
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292397"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Dernière version du [kit SDK .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pour votre système d’exploitation.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).
- Créez une [ressource Application Insights](../../../azure-monitor/app/create-new-resource.md) dans le portail Azure.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `TelemetryAppInsightsQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o TelemetryAppInsightsQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd TelemetryAppInsightsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Alors que vous êtes toujours dans le répertoire de l’application, installez le package de la bibliothèque Azure Communication Services Identity pour .NET à l’aide de la commande `dotnet add package`.

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

Vous devez également installer l’exportateur Azure Monitor pour la bibliothèque OpenTelemetry.

```console
dotnet add package Azure.Monitor.OpenTelemetry.Exporter
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez le fichier **Program.cs** dans un éditeur de texte
2. Ajoutez une directive `using` pour inclure l’espace de noms `Azure.Communication.Identity`
3. Ajoutez une directive `using` pour inclure l’espace de noms `Azure.Monitor.OpenTelemetry.Exporter` ;
4. Ajoutez une directive `using` pour inclure l’espace de noms `OpenTelemetry.Trace` ;
5. Mettez à jour la déclaration de méthode `Main` pour prendre en charge le code asynchrone

Utilisez le code suivant pour commencer :

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry.Trace;

namespace TelemetryAppInsightsQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Export Telemetry to Application Insights");

            // Quickstart code goes here
        }
    }
}
```
## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>Configuration du traceur de télémétrie avec des appels au Kit de développement logiciel (SDK) d’identité de communication

Initialisez un `CommunicationIdentityClient` avec votre chaîne de connexion. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Une fois le client créé, vous devez définir une `Activity Source` qui suivra toutes les activités. Ensuite, vous pouvez utiliser la `Activity Source` pour démarrer une `Activity` qui sera utilisée pour suivre l’appel au Kit de développement logiciel (SDK) `CreateUserAsync`. Notez que vous pouvez également définir des propriétés personnalisées à suivre dans chaque `Activity` l’aide de la méthode `SetTag`.

Un modèle de traçage similaire est élaboré pour la fonction `GetTokenAsync`.

Créez une fonction appelée `TracedSample` et ajoutez le code suivant :

```csharp
public static async Task TracedSample()
{
    // This code demonstrates how to fetch your connection string
    // from an environment variable.
    string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
    var client = new CommunicationIdentityClient(connectionString);

    using var source = new ActivitySource("Quickstart.IdentityTelemetry");
    CommunicationUserIdentifier identity = null;

    using (var activity = source.StartActivity("Create User Activity"))
    {
        var identityResponse = await client.CreateUserAsync();

        identity = identityResponse.Value;
        Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
        activity?.SetTag("Identity id", identity.Id);
    }

    using (var activity = source.StartActivity("Get Token Activity"))
    {
        var tokenResponse = await client.GetTokenAsync(identity, scopes: new[] { CommunicationTokenScope.Chat });

        activity?.SetTag("Token value", tokenResponse.Value.Token);
        activity?.SetTag("Expires on", tokenResponse.Value.ExpiresOn);

        Console.WriteLine($"\nIssued an access token with 'chat' scope that expires at {tokenResponse.Value.ExpiresOn}:");
    }
}
```

## <a name="funneling-telemetry-data-to-application-insights"></a>Canalisation des données de télémétrie vers Application Insights

Une fois les appels au Kit de développement logiciel (SDK) inclus dans un wrapper avec les activités, vous pouvez ajouter l’exportateur de trace OpenTelemetry et canaliser les données vers la ressource Application Insights.

Vous avez la possibilité de définir un dictionnaire avec des attributs de ressource qui s’affichent dans Application Insights.
Ensuite, appelez `AddSource` et utilisez le nom de source d’activité défini dans `TracedSample`.
Vous devez également récupérer la chaîne de connexion de votre ressource Application Insights et la transmettre à `AddAzureMonitorTraceExporter()`. Cela a pour effet de canaliser les données de télémétrie vers votre ressource Application Insights.

Enfin, appelez et attendez la fonction `TracedSample()` où nous avons nos appels au Kit de développement logiciel (SDK).

Ajoutez le code suivant à la méthode `Main` :

```csharp
var resourceAttributes = new Dictionary<string, object> { { "service.name", "<service-name>" }, { "service.instance.id", "<service-instance-id>" } };
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);

using var tracerProvider = Sdk.CreateTracerProviderBuilder()
    .SetResourceBuilder(resourceBuilder)
    .AddSource("Quickstart.IdentityTelemetry")
    .AddAzureMonitorTraceExporter(o =>
    {
        o.ConnectionString = Environment.GetEnvironmentVariable("APPLICATION_INSIGHTS_CONNECTION_STRING");
    })
    .Build();

await TracedSample();
```

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```