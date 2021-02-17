---
title: Signer une requête HTTP avec C#
description: Il s’agit de la version C# de la signature d’une requête HTTP avec une signature HMAC pour Communication Services.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 1985ae92b68c16798fc26d7517520c0a70a2ad28
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628291"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :
- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installez [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer une ressource Azure Communication](../../quickstarts/create-communication-resource.md). Vous devez enregistrer vos valeurs **resourceEndpoint** et **resourceAccessKey** pour ce tutoriel.



## <a name="sign-an-http-request-with-c"></a>Signer une requête HTTP avec C#
L’authentification par clé d’accès utilise une clé secrète partagée afin de générer une signature HMAC pour chaque requête HTTP. Cette signature est générée avec l’algorithme SHA256 et est envoyée dans l’en-tête `Authorization` à l’aide du schéma `HMAC-SHA256`. Par exemple :

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

La signature `hmac-sha256-signature` est constituée des éléments suivants : 

- Un verbe HTTP (par exemple, `GET` ou `PUT`)
- Un chemin de requête HTTP
- Date
- Host
- x-ms-content-sha256

## <a name="setting-up"></a>Configuration
Les étapes suivantes décrivent comment construire l’en-tête d’authorisation :

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `SignHmacTutorial`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Installer le package

Installez le package `Newtonsoft.Json`, utilisé pour la sérialisation du corps :

```console
dotnet add package Newtonsoft.Json
```

Mettez à jour la déclaration de la méthode `Main` pour prendre en charge le code asynchrone. Utilisez le code suivant pour commencer :

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Créer un message de requête

Pour cet exemple, nous allons signer une demande de création d’une identité à l’aide de l’API Authentification Communication Services (version `2020-07-20-preview2`)

Ajoutez le code suivant à la méthode `Main` :

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2020-07-20-preview2");
//Endpoint identities?api-version=2020-07-20-preview2 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Remplacez `resourceEndpoint` par votre valeur de point de terminaison de ressource réelle.

## <a name="create-content-hash"></a>Créer un hachage de contenu

Le hachage de contenu fait partie de votre signature HMAC. Utilisez le code suivant pour calculer le hachage de contenu. Vous pouvez ajouter cette méthode à `Progam.cs` sous la méthode `Main`.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Calculer une signature
Utilisez le code suivant pour créer une méthode destinée à calculer votre signature HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Remplacez `resourceAccessKey` par la clé d’accès de votre ressource Azure Communication Services réelle.

## <a name="create-an-authorization-header-string"></a>Créer une chaîne d’en-tête d’autorisation

Nous allons maintenant construire la chaîne que nous ajouterons à notre en-tête d’autorisation :

1. Calculer un hachage de contenu (Compute a content hash)
2. Spécifier l’horodatage en Temps universel (UTC, Universal Time Coordinated) (Specify the Coordinated Universal Time (UTC) timestamp)
3. Préparer une chaîne à signer (Prepare a string to sign)
4. Calculer la signature (Compute the signature)
5. Concaténer la chaîne qui sera utilisée dans l’en-tête d’autorisation (Concatenate the string, which will be used in authorization header)
 
Ajoutez le code suivant à la méthode `Main` :

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Ajouter des en-têtes à requestMessage

Utilisez le code suivant pour ajouter les en-têtes obligatoires à votre `requestMessage` :

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Tester le client
Appelez le point de terminaison à l’aide de `HttpClient`, puis vérifiez la réponse.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
