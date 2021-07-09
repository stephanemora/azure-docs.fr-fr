---
title: Fichier include
description: Fichier include
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 05/21/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: fa3ecf3fa6b74bdbd52a9b077cde04c6f98dde86
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026241"
---
### <a name="prerequisite-check"></a>Vérification du prérequis

- Dans une fenêtre de terminal ou de commande, exécutez la commande `dotnet` pour vérifier que le kit de développement logiciel (SDK) .NET est installé.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

1. Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `RelayTokenQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o RelayTokenQuickstart
```

1. Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd RelayTokenQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

En restant dans le répertoire de l’application, installez le package de bibliothèques Azure Communication Services Identity et NetworkTraversal pour .NET en utilisant la commande `dotnet add package`.

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.NetworkTraversal
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez le fichier **Program.cs** dans un éditeur de texte
2. Ajoutez une directive `using` pour inclure `Azure.Communication.Identity`, `System.Threading.Tasks` et `System.Net.Http`.
3. Mettez à jour la déclaration de méthode `Main` pour prendre en charge le code asynchrone

Voici le code :

```csharp
using System;
using Azure.Communication.Identity;
using Azure;
using System.Collections.Generic;
using Azure.Communication.NetworkTraversal;

namespace RelayTokenQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Relay Token Quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="authenticate-the-client"></a>Authentifier le client

Initialisez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

Ajoutez le code suivant à la méthode `Main` :

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Créer une identité

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `createUser` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Stockez l’identité reçue avec un mappage aux utilisateurs de votre application. Par exemple, en les stockant dans la base de données de votre serveur d’applications. L’identité sera demandée ultérieurement pour émettre des jetons d’accès.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>Échanger le jeton d’accès utilisateur pour un jeton de relais

Appeler le service de jeton Azure Communication pour échanger le jeton d’accès utilisateur pour un jeton de service TURN

```csharp
var relayClient = new CommunicationRelayClient("COMMUNICATION_SERVICES_CONNECTION_STRING");

Response<CommunicationRelayConfiguration> turnTokenResponse = await relayClient.GetRelayConfigurationAsync(identity);
DateTimeOffset turnTokenExpiresOn = turnTokenResponse.Value.ExpiresOn;
IReadOnlyList<CommunicationTurnServer> turnServers = turnTokenResponse.Value.TurnServers;
Console.WriteLine($"Expires On: {turnTokenExpiresOn}");
foreach (CommunicationTurnServer turnServer in turnServers)
{
    foreach (string url in turnServer.Urls)
    {
        Console.WriteLine($"TURN Url: {url}");
    }
    Console.WriteLine($"TURN Username: {turnServer.Username}");
    Console.WriteLine($"TURN Credential: {turnServer.Credential}");
}
```

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```
