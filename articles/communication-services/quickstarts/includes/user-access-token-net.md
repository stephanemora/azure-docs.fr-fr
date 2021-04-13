---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 653692812507c05c6cfc58b00d3c93ece19019bb
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113063"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Dernière version du [kit SDK .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pour votre système d’exploitation.
- Une ressource Communication Services active et la chaîne de connexion. [Créez une ressource Communication Services](../create-communication-resource.md).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `AccessTokensQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Alors que vous êtes toujours dans le répertoire de l’application, installez le package de la bibliothèque Azure Communication Services Identity pour .NET à l’aide de la commande `dotnet add package`.

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

À partir du répertoire de projet :

1. Ouvrez le fichier **Program.cs** dans un éditeur de texte
1. Ajoutez une directive `using` pour inclure l’espace de noms `Azure.Communication.Identity`
1. Mettez à jour la déclaration de méthode `Main` pour prendre en charge le code asynchrone

Utilisez le code suivant pour commencer :

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>Authentifier le client

Initialisez un `CommunicationIdentityClient` avec votre chaîne de connexion. Le code ci-dessous récupère la chaîne de connexion de la ressource à partir d’une variable d’environnement nommée `COMMUNICATION_SERVICES_CONNECTION_STRING`. Découvrez comment [gérer la chaîne de connexion de la ressource](../create-communication-resource.md#store-your-connection-string).

Ajoutez le code suivant à la méthode `Main` :

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(connectionString);
```

Vous pouvez également séparer le point de terminaison et la clé d’accès.
```csharp
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
string endpoint = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ENDPOINT");
string accessKey = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_ACCESSKEY");
var client = new CommunicationIdentityClient(new Uri(endpoint), new AzureKeyCredential(accessKey));
```

Si vous avez une identité managée configurée, consultez [Utiliser des identités managées](../managed-identity.md). Vous pouvez également vous authentifier avec une identité managée.
```csharp
TokenCredential tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Créer une identité

Azure Communication Services gère un répertoire LID (Lightweight Identity Directory). Utilisez la méthode `createUser` pour créer une entrée dans le répertoire avec une valeur `Id` unique. Stockez l’identité reçue avec un mappage aux utilisateurs de votre application. Par exemple, en les stockant dans la base de données de votre serveur d’applications. L’identité sera demandée ultérieurement pour émettre des jetons d’accès.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Émettre des jetons d’accès d’identité

Avec la méthode `GetToken`, émettez un jeton d’accès pour une identité Communication Services existant déjà. Le paramètre `scopes` définit un ensemble de primitives, qui autorise ce jeton d’accès. Consultez [la liste des actions prises en charge](../../concepts/authentication.md). Une nouvelle instance du paramètre `communicationUser` peut être construite en fonction de la représentation sous forme de chaîne de l’identité Azure Communication Service.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Les jetons d’accès sont des informations d’identification à durée de vie courte, qui doivent être réémises. Ne pas le faire peut entraîner une interruption expérimentée par les utilisateurs de votre application. La propriété de réponse `expiresOn` indique la durée de vie du jeton d’accès.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Créer une identité et émettre un jeton d’accès dans la même requête

La méthode `CreateUserAndTokenAsync` permet de créer une identité Communication Services et d’émettre un jeton d’accès pour celle-ci. Le paramètre `scopes` définit un ensemble de primitives, qui autorise ce jeton d’accès. Consultez [la liste des actions prises en charge](../../concepts/authentication.md).

```csharp
// Issue an identity and an access token with the "voip" scope for the new identity
var identityAndTokenResponse = await client.CreateUserAndTokenAsync(scopes: new[] { CommunicationTokenScope.VoIP });
var identity = identityAndTokenResponse.Value.User;
var token = identityAndTokenResponse.Value.AccessToken.Token;
var expiresOn = identityAndTokenResponse.Value.AccessToken.ExpiresOn;

Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

## <a name="refresh-access-tokens"></a>Actualiser des jetons d’accès

Pour actualiser un jeton d’accès, transmettez une instance de l’objet `CommunicationUserIdentifier` dans `GetTokenAsync`. Si vous avez stocké cet élément `Id` et que vous devez créer un `CommunicationUserIdentifier`, vous pouvez le faire en passant votre `Id` stocké dans le constructeur `CommunicationUserIdentifier`, comme suit :

```csharp
var identityToRefresh = new CommunicationUserIdentifier(identity.Id);
var tokenResponse = await client.GetTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Révoquer des jetons d’accès

Dans certains cas, vous pouvez révoquer explicitement des jetons d’accès. Par exemple, lorsqu’un utilisateur d’une application modifie le mot de passe qu’il utilise pour s’authentifier auprès de votre service. La méthode `RevokeTokensAsync` invalide tous les jetons d’accès actifs qui ont été émis pour l’identité.

```csharp
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Supprimer une identité

Quand vous supprimez une identité, vous supprimez aussi tous les jetons d’accès actifs et vous ne pouvez plus émettre de jetons d’accès pour les identités. Tout le contenu persistant associé à l’identité est également supprimé.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```
