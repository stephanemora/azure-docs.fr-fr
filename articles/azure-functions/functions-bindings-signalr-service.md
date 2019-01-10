---
title: Liaisons de service SignalR Azure Functions
description: Découvrez comment utiliser les liaisons de service SignalR dans Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 7a7063b9177774c5207746283dc7cd25e3dd5793
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721884"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Liaisons de service SignalR pour Azure Functions

Cet article explique comment authentifier et envoyer des messages en temps réel aux clients connectés à [Service SignalR Azure](https://azure.microsoft.com/services/signalr-service/) à l’aide de liaisons de service SignalR dans Azure Functions. Azure Functions prend en charge des liaisons d’entrée et de sortie pour le service SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Les liaisons de service SignalR sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService), version 1.0.0-preview1-*. Le code source pour le package se trouve dans le référentiel GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

> [!NOTE]
> Le service SignalR Azure est mis à la disposition générale. Toutefois, les liaisons de service SignalR pour Azure Functions sont actuellement en préversion.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>Liaison d’entrée d’informations de connexion SignalR

Avant qu’un client puisse se connecter au service SignalR Azure, il doit récupérer l’URL du point de terminaison du service et un jeton d’accès valide. La liaison d’entrée *SignalRConnectionInfo* génère l’URL du point de terminaison du service SignalR et un jeton valide qui sont utilisés pour se connecter au service. Le jeton étant limité dans le temps et pouvant être utilisé pour authentifier un utilisateur spécifique sur une connexion, vous ne devez pas mettre le jeton en cache ou le partager entre plusieurs clients. Un déclencheur HTTP utilisant cette liaison peut être utilisé par les clients pour récupérer les informations de connexion.

Consultez l’exemple propre à un langage particulier :

* [C# 2.x](#2x-c-input-example)
* [JavaScript 2.x](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>Exemple d’entrée C# 2.x

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui acquiert des informations de connexion SignalR à l’aide de la liaison d’entrée et les renvoie via HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Jetons authentifiés

Si la fonction est déclenchée par un client authentifié, vous pouvez ajouter une revendication d’ID d’utilisateur au jeton généré. Vous pouvez facilement ajouter l’authentification à une application de fonction à l’aide de [Authentification App Service] (../app-service/overview-authentication-authorization.md).

L’authentification App Service définit les en-têtes HTTP nommés `x-ms-client-principal-id` et `x-ms-client-principal-name` qui contiennent l’identité principale du client et le nom de l’utilisateur authentifié, respectivement. Vous pouvez définir la propriété `UserId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](functions-triggers-bindings.md#binding-expressions-and-patterns) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>Exemple d’entrée JavaScript 2.x

L’exemple suivant montre une liaison d’entrée d’informations de connexion SignalR dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison pour retourner les informations de connexion.

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Jetons authentifiés

Si la fonction est déclenchée par un client authentifié, vous pouvez ajouter une revendication d’ID d’utilisateur au jeton généré. Vous pouvez facilement ajouter l’authentification à une application de fonction à l’aide de [Authentification App Service] (../app-service/overview-authentication-authorization.md).

L’authentification App Service définit les en-têtes HTTP nommés `x-ms-client-principal-id` et `x-ms-client-principal-name` qui contiennent l’identité principale du client et le nom de l’utilisateur authentifié, respectivement. Vous pouvez définir la propriété `userId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](functions-triggers-bindings.md#binding-expressions-and-patterns) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

Exemple de fichier function.json :

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>Liaison de sortie SignalR

Utilisez la liaison de sortie *SignalR* pour envoyer un ou plusieurs messages à l’aide du service SignalR Azure. Vous pouvez diffuser un message à tous les clients connectés, ou vous pouvez le diffuser uniquement aux clients connectés qui ont été authentifiés comme un utilisateur donné.

Consultez l’exemple propre à un langage particulier :

* [C# 2.x](#2x-c-output-example)
* [JavaScript 2.x](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>Exemple de sortie C# 2.x

#### <a name="broadcast-to-all-clients"></a>Diffuser à tous les clients

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui envoie un message à l’aide de la liaison de sortie à tous les clients connectés. `Target` correspond au nom de la méthode à appeler sur chaque client. La propriété `Arguments` est un tableau de zéro ou plusieurs objets à transmettre à la méthode du client.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Envoyer à un utilisateur

Vous ne pouvez envoyer un message qu’aux connexions qui ont été authentifiées comme un utilisateur en définissant la propriété `UserId` du message SignalR.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>Exemple de sortie JavaScript 2.x

#### <a name="broadcast-to-all-clients"></a>Diffuser à tous les clients

L’exemple suivant montre une liaison de sortie SignalR dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison pour envoyer un message avec le service SignalR Azure. Définissez la liaison de sortie sur un tableau d’un ou plusieurs messages SignalR. Un message SignalR se compose d’une propriété `target` qui spécifie le nom de la méthode à appeler sur chaque client, et d’une propriété `arguments` qui correspond à un tableau d’objets à transmettre à la méthode du client en tant qu’arguments.

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Envoyer à un utilisateur

Vous ne pouvez envoyer un message qu’aux connexions qui ont été authentifiées comme un utilisateur en définissant la propriété `userId` du message SignalR.

Le fichier *function.json* ne change pas. Voici le code JavaScript :

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Configuration

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `SignalRConnectionInfo`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**|| Cette propriété doit être définie sur `signalRConnectionInfo`.|
|**direction**|| Cette propriété doit être définie sur `in`.|
|**name**|| Nom de variable utilisé dans le code de fonction pour l’objet d’informations de connexion. |
|**hubName**|**HubName**| Cette valeur doit être définie sur le nom du hub SignalR pour lequel les informations de connexion sont générées.|
|**userId**|**UserId**| Facultatif : valeur de la revendication d’identification d’utilisateur à définir dans le jeton de clé d’accès. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nom du paramètre d’application contenant la chaîne de connexion du service SignalR (« AzureSignalRConnectionString » par défaut) |

### <a name="signalr"></a>SignalR

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `SignalR`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**|| Cette propriété doit être définie sur `signalR`.|
|**direction**|| Cette propriété doit être définie sur `out`.|
|**name**|| Nom de variable utilisé dans le code de fonction pour l’objet d’informations de connexion. |
|**hubName**|**HubName**| Cette valeur doit être définie sur le nom du hub SignalR pour lequel les informations de connexion sont générées.|
|**connectionStringSetting**|**ConnectionStringSetting**| Nom du paramètre d’application contenant la chaîne de connexion du service SignalR (« AzureSignalRConnectionString » par défaut) |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)

