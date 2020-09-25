---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 12a8bccbf9c177c92160d52f4506618d03c791e6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944579"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- La dernière version de la [bibliothèque de client .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pour votre système d’exploitation.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `UserAccessTokensQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Toujours dans le répertoire de l’application, installez le package de la bibliothèque de client Azure Communication Services Administration pour .NET à l’aide de la commande `dotnet add package`.

```console
dotnet add package Azure.Communication.Administration
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez le fichier **Program.cs** dans un éditeur de texte
1. Ajoutez une directive `using` pour inclure l’espace de noms `Azure.Communication.Administration`
1. Mettez à jour la déclaration de méthode `Main` pour prendre en charge le code asynchrone

Utilisez le code suivant pour commencer :

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Authentifier le client

Initialisez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `Main` :

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Créer un utilisateur

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `createUser` pour créer une entrée, avec une valeur `Id` unique, dans le répertoire. Vous devez tenir à jour un mappage entre les utilisateurs de votre application et les identités générées par Communication Services (par exemple, en les stockant dans la base de données de votre serveur d’applications).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Émettre des jetons d’accès utilisateur

Avec la méthode `issueToken`, émettez un jeton d’accès pour un utilisateur de Communication Services. Si vous ne spécifiez pas le paramètre facultatif `user`, un nouvel utilisateur sera créé et retourné avec le jeton.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Les jetons d’accès utilisateur sont des informations d’identification de courte durée qui doivent être réémises afin d’éviter que les utilisateurs rencontrent des interruptions de service. La propriété de réponse `expiresOn` indique la durée de vie du jeton.

## <a name="revoke-user-access-tokens"></a>Révoquer des jetons d’accès utilisateur

Dans certains cas, il est nécessaire de révoquer explicitement des jetons d’accès utilisateur, par exemple, quand des utilisateurs changent leur mot de passe pour s’authentifier auprès de votre service. Cette fonctionnalité est disponible à partir de la bibliothèque de client Azure Communication Services Administration.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Supprimer un utilisateur

Quand vous supprimez une identité, vous supprimez aussi tous les jetons actifs et vous ne pouvez plus ensuite émettre de jetons pour les identités. De plus, tout le contenu persistant associé à l’utilisateur est également supprimé.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```
