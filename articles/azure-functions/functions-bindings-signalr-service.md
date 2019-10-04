---
title: Liaisons de service SignalR Azure Functions
description: Découvrez comment utiliser les liaisons de service SignalR dans Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur
ms.service: azure-functions
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 82bbd728bfd51595f0b6eacf344bc95ea5db1ea8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086157"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Liaisons de service SignalR pour Azure Functions

Cet article explique comment authentifier et envoyer des messages en temps réel aux clients connectés à [Service SignalR Azure](https://azure.microsoft.com/services/signalr-service/) à l’aide de liaisons de service SignalR dans Azure Functions. Azure Functions prend en charge des liaisons d’entrée et de sortie pour le service SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Packages - Functions 2.x

Les liaisons de service SignalR sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService), version 1.*. Le code source pour le package se trouve dans le référentiel GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Annotations Java

Pour utiliser les annotations SignalR Service dans les fonctions Java, vous devez ajouter une dépendance à l’artefact *azure-fonctions-java-bibliothèque-signalr* (version 1.0 ou version ultérieure) à votre fichier pom.xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Pour utiliser les liaisons de SignalR Service en Java, assurez-vous que vous utilisez la version 2.4.419 ou une version ultérieure d’Azure Functions Core Tools (version de l’hôte 2.0.12332).

## <a name="using-signalr-service-with-azure-functions"></a>Utilisation de SignalR Service avec Azure Functions

Pour plus d’informations sur la configuration et l’utilisation de SignalR Service et Azure Functions ensemble, reportez-vous à [Développement et configuration Azure Functions avec Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

## <a name="signalr-connection-info-input-binding"></a>Liaison d’entrée d’informations de connexion SignalR

Avant qu’un client puisse se connecter au service SignalR Azure, il doit récupérer l’URL du point de terminaison du service et un jeton d’accès valide. La liaison d’entrée *SignalRConnectionInfo* génère l’URL du point de terminaison du service SignalR et un jeton valide qui sont utilisés pour se connecter au service. Le jeton étant limité dans le temps et pouvant être utilisé pour authentifier un utilisateur spécifique sur une connexion, vous ne devez pas mettre le jeton en cache ou le partager entre plusieurs clients. Un déclencheur HTTP utilisant cette liaison peut être utilisé par les clients pour récupérer les informations de connexion.

Consultez l’exemple propre à un langage particulier :

* [C# 2.x](#2x-c-input-examples)
* [JavaScript 2.x](#2x-javascript-input-examples)
* [Java 2.x](#2x-java-input-examples)

Pour plus d’informations sur la façon dont cette liaison est utilisée pour créer une fonction « negotiate » qui peut être utilisée par un client SignalR, consultez l’[article sur le développement et la configuration d’Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) dans la documentation sur les concepts de SignalR Service.

### <a name="2x-c-input-examples"></a>Exemples d’entrée C# 2.x

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui acquiert des informations de connexion SignalR à l’aide de la liaison d’entrée et les renvoie via HTTP.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Jetons authentifiés

Si la fonction est déclenchée par un client authentifié, vous pouvez ajouter une revendication d’ID d’utilisateur au jeton généré. Vous pouvez facilement ajouter l’authentification à une application de fonction à l’aide de [Authentification App Service](../app-service/overview-authentication-authorization.md).

L’authentification App Service définit les en-têtes HTTP nommés `x-ms-client-principal-id` et `x-ms-client-principal-name` qui contiennent l’identité principale du client et le nom de l’utilisateur authentifié, respectivement. Vous pouvez définir la propriété `UserId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-examples"></a>Exemples d’entrée JavaScript 2.x

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
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>Jetons authentifiés

Si la fonction est déclenchée par un client authentifié, vous pouvez ajouter une revendication d’ID d’utilisateur au jeton généré. Vous pouvez facilement ajouter l’authentification à une application de fonction à l’aide de [Authentification App Service](../app-service/overview-authentication-authorization.md).

L’authentification App Service définit les en-têtes HTTP nommés `x-ms-client-principal-id` et `x-ms-client-principal-name` qui contiennent l’identité principale du client et le nom de l’utilisateur authentifié, respectivement. Vous pouvez définir la propriété `userId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`. 

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
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>Exemples d’entrée Java 2.x

L’exemple suivant montre une [fonction Java](functions-reference-java.md) qui acquiert des informations de connexion SignalR à l’aide de la liaison d’entrée et les renvoie via HTTP.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Jetons authentifiés

Si la fonction est déclenchée par un client authentifié, vous pouvez ajouter une revendication d’ID d’utilisateur au jeton généré. Vous pouvez facilement ajouter l’authentification à une application de fonction à l’aide de [Authentification App Service](../app-service/overview-authentication-authorization.md).

L’authentification App Service définit les en-têtes HTTP nommés `x-ms-client-principal-id` et `x-ms-client-principal-name` qui contiennent l’identité principale du client et le nom de l’utilisateur authentifié, respectivement. Vous pouvez définir la propriété `UserId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>Liaison de sortie SignalR

Utilisez la liaison de sortie *SignalR* pour envoyer un ou plusieurs messages à l’aide du service SignalR Azure. Vous pouvez diffuser un message à tous les clients connectés, ou vous pouvez le diffuser uniquement aux clients connectés qui ont été authentifiés comme un utilisateur donné.

Vous pouvez également l’utiliser pour gérer les groupes auxquels appartient un utilisateur.

Consultez l’exemple propre à un langage particulier :

* [C# 2.x](#2x-c-send-message-output-examples)
* [JavaScript 2.x](#2x-javascript-send-message-output-examples)
* [Java 2.x](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>Exemples de messages de sortie C# 2.x

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
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>Envoyer à un groupe

Vous ne pouvez envoyer un message qu’aux connexions qui ont été ajoutées à un groupe en définissant la propriété `GroupName` du message SignalR.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>Exemples de sortie de gestion de groupe C# 2.x

SignalR Service permet aux utilisateurs d’être ajoutés à des groupes. Des messages peuvent ensuite être envoyés au groupe. Vous pouvez utiliser la classe `SignalRGroupAction` avec la sortie de liaison `SignalR` pour gérer l’appartenance au groupe d’un utilisateur.

#### <a name="add-user-to-a-group"></a>Ajouter un utilisateur à un groupe

L’exemple suivant ajoute un utilisateur à un groupe.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>Supprimer un utilisateur d’un groupe

L’exemple suivant supprime un utilisateur d’un groupe.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    var userIdClaim = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier);
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userIdClaim.Value,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

> [!NOTE]
> Pour obtenir la liaison correcte de `ClaimsPrincipal`, vous devez avoir configuré les paramètres d’authentification dans Azure Functions.

### <a name="2x-javascript-send-message-output-examples"></a>Exemples de messages de sortie JavaScript 2.x

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
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>Envoyer à un utilisateur

Vous ne pouvez envoyer un message qu’aux connexions qui ont été authentifiées comme un utilisateur en définissant la propriété `userId` du message SignalR.

Le fichier *function.json* ne change pas. Voici le code JavaScript :

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>Envoyer à un groupe

Vous ne pouvez envoyer un message qu’aux connexions qui ont été ajoutées à un groupe en définissant la propriété `groupName` du message SignalR.

Le fichier *function.json* ne change pas. Voici le code JavaScript :

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>Exemples de sortie de gestion de groupe JavaScript 2.x

SignalR Service permet aux utilisateurs d’être ajoutés à des groupes. Des messages peuvent ensuite être envoyés au groupe. Vous pouvez utiliser la sortie de liaison `SignalR` pour gérer l’appartenance au groupe d’un utilisateur.

#### <a name="add-user-to-a-group"></a>Ajouter un utilisateur à un groupe

L’exemple suivant ajoute un utilisateur à un groupe.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>Supprimer un utilisateur d’un groupe

L’exemple suivant supprime un utilisateur d’un groupe.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>Exemples de messages de sortie Java 2.x

#### <a name="broadcast-to-all-clients"></a>Diffuser à tous les clients

L’exemple suivant montre une [fonction Java](functions-reference-java.md) qui envoie un message à l’aide de la liaison de sortie à tous les clients connectés. `target` correspond au nom de la méthode à appeler sur chaque client. La propriété `arguments` est un tableau de zéro ou plusieurs objets à transmettre à la méthode du client.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>Envoyer à un utilisateur

Vous ne pouvez envoyer un message qu’aux connexions qui ont été authentifiées comme un utilisateur en définissant la propriété `userId` du message SignalR.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>Envoyer à un groupe

Vous ne pouvez envoyer un message qu’aux connexions qui ont été ajoutées à un groupe en définissant la propriété `groupName` du message SignalR.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>Exemples de sortie de gestion de groupe Java 2.x

SignalR Service permet aux utilisateurs d’être ajoutés à des groupes. Des messages peuvent ensuite être envoyés au groupe. Vous pouvez utiliser la classe `SignalRGroupAction` avec la sortie de liaison `SignalROutput` pour gérer l’appartenance au groupe d’un utilisateur.

#### <a name="add-user-to-a-group"></a>Ajouter un utilisateur à un groupe

L’exemple suivant ajoute un utilisateur à un groupe.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>Supprimer un utilisateur d’un groupe

L’exemple suivant supprime un utilisateur d’un groupe.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
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

> [!div class="nextstepaction"]
> [Développement et configuration Azure Functions avec Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
