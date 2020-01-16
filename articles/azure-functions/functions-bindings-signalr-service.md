---
title: Liaisons de service SignalR Azure Functions
description: Découvrez comment utiliser les liaisons de service SignalR dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: cbc45036497547b78514b1d175d67c6ed437ee2d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769487"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Liaisons de service SignalR pour Azure Functions

Cet article explique comment authentifier et envoyer des messages en temps réel aux clients connectés à [Service SignalR Azure](https://azure.microsoft.com/services/signalr-service/) à l’aide de liaisons de service SignalR dans Azure Functions. Azure Functions prend en charge des liaisons d’entrée et de sortie pour le service SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages – Functions 2.x et versions ultérieures

Les liaisons de service SignalR sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService), version 1.*. Le code source pour le package se trouve dans le référentiel GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

Pour plus d’informations sur la configuration et l’utilisation de SignalR Service et Azure Functions ensemble, reportez-vous à [Développement et configuration Azure Functions avec Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md).

### <a name="annotations-library-java-only"></a>Bibliothèque d’annotations (Java uniquement)

Pour utiliser les annotations SignalR Service dans les fonctions Java, vous devez ajouter une dépendance à l’artefact *azure-fonctions-java-bibliothèque-signalr* (version 1.0 ou version ultérieure) à votre fichier pom.xml.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="input"></a>Entrée

Avant qu’un client puisse se connecter au service SignalR Azure, il doit récupérer l’URL du point de terminaison du service et un jeton d’accès valide. La liaison d’entrée *SignalRConnectionInfo* génère l’URL du point de terminaison du service SignalR et un jeton valide qui sont utilisés pour se connecter au service. Le jeton étant limité dans le temps et pouvant être utilisé pour authentifier un utilisateur spécifique sur une connexion, vous ne devez pas mettre le jeton en cache ou le partager entre plusieurs clients. Un déclencheur HTTP utilisant cette liaison peut être utilisé par les clients pour récupérer les informations de connexion.

Pour plus d’informations sur la façon dont cette liaison est utilisée pour créer une fonction « negotiate » qui peut être utilisée par un client SignalR, consultez l’[article sur le développement et la configuration d’Azure Functions](../azure-signalr/signalr-concept-serverless-development-config.md) dans la documentation sur les concepts de SignalR Service.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

L’exemple suivant montre une liaison d’entrée d’informations de connexion SignalR dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison pour retourner les informations de connexion.

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

Voici le code de script C# :

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple suivant montre une liaison d’entrée d’informations de connexion SignalR dans un fichier *function.json* et une [fonction Python](functions-reference-python.md) qui utilise la liaison pour retourner les informations de connexion.

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

Voici le code Python :

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="authenticated-tokens"></a>Jetons authentifiés

Si la fonction est déclenchée par un client authentifié, vous pouvez ajouter une revendication d’ID d’utilisateur au jeton généré. Vous pouvez facilement ajouter l’authentification à une application de fonction à l’aide de [Authentification App Service](../app-service/overview-authentication-authorization.md).

L’authentification App Service définit les en-têtes HTTP nommés `x-ms-client-principal-id` et `x-ms-client-principal-name` qui contiennent l’identité principale du client et le nom de l’utilisateur authentifié, respectivement.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Vous pouvez définir la propriété `UserId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

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

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

Vous pouvez définir la propriété `userId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

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

Voici le code de script C# :

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static SignalRConnectionInfo Run(HttpRequest req, SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    return connectionInfo;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Vous pouvez définir la propriété `userId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Vous pouvez définir la propriété `userId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

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

Voici le code Python :

```python
def main(req: func.HttpRequest, connectionInfoJson: str) -> func.HttpResponse:
    # connectionInfo contains an access key token with a name identifier
    # claim set to the authenticated user
    return func.HttpResponse(
        connectionInfoJson,
        status_code=200,
        headers={
            'Content-type': 'application/json'
        }
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

Vous pouvez définir la propriété `userId` de la liaison sur la valeur de l’un des en-têtes à l’aide d’une [expression de liaison](./functions-bindings-expressions-patterns.md) : `{headers.x-ms-client-principal-id}` ou `{headers.x-ms-client-principal-name}`.

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

---

## <a name="output"></a>Output

Utilisez la liaison de sortie *SignalR* pour envoyer un ou plusieurs messages à l’aide du service SignalR Azure. Vous pouvez diffuser un message à tous les clients connectés, ou vous pouvez le diffuser uniquement aux clients connectés qui ont été authentifiés comme un utilisateur donné.

Vous pouvez également l’utiliser pour gérer les groupes auxquels appartient un utilisateur.

### <a name="broadcast-to-all-clients"></a>Diffuser à tous les clients

L’exemple suivant montre une fonction qui envoie un message à l’aide de la liaison de sortie à tous les clients connectés. La *cible* correspond au nom de la méthode à appeler sur chaque client. *Arguments* est un tableau de zéro objet ou plus à transmettre à la méthode du client.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

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

Voici le code de script C# :

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Voici le code Python :

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="send-to-a-user"></a>Envoyer à un utilisateur

Vous ne pouvez envoyer un message qu’aux connexions qui ont été authentifiées comme un utilisateur en définissant l’*ID d’utilisateur* du message SignalR.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

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

Voici le code de script C# :

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Voici le code Python :

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this user ID
        'userId': 'userId1',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="send-to-a-group"></a>Envoyer à un groupe

Vous ne pouvez envoyer un message qu’aux connexions qui ont été ajoutées à un groupe en définissant le *nom de groupe* du message SignalR.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

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

Voici le code de script C# :

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    object message,
    IAsyncCollector<SignalRMessage> signalRMessages)
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "signalR",
  "name": "out_message",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Voici le code Python :

```python
def main(req: func.HttpRequest, out_message: func.Out[str]) -> func.HttpResponse:
    message = req.get_json()
    out_message.set(json.dumps({
        #message will only be sent to this group
        'groupName': 'myGroup',
        'target': 'newMessage',
        'arguments': [ message ]
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

### <a name="group-management"></a>Gestion des groupes

SignalR Service permet aux utilisateurs d’être ajoutés à des groupes. Des messages peuvent ensuite être envoyés au groupe. Vous pouvez utiliser la sortie de liaison `SignalR` pour gérer l’appartenance au groupe d’un utilisateur.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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

# <a name="c-scripttabcsharp-script"></a>[Script C#](#tab/csharp-script)

#### <a name="add-user-to-a-group"></a>Ajouter un utilisateur à un groupe

L’exemple suivant ajoute un utilisateur à un groupe.

Exemple de fichier *function.json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run.csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
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

Exemple de fichier *function.json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*Run.csx*

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using Microsoft.Azure.WebJobs.Extensions.SignalRService;

public static Task Run(
    HttpRequest req,
    ClaimsPrincipal claimsPrincipal,
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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

#### <a name="add-user-to-a-group"></a>Ajouter un utilisateur à un groupe

L’exemple suivant ajoute un utilisateur à un groupe.

Exemple de fichier *function.json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
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

Exemple de fichier *function.json*

```json
{
    "type": "signalR",
    "name": "signalRGroupActions",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
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

# <a name="pythontabpython"></a>[Python](#tab/python)

#### <a name="add-user-to-a-group"></a>Ajouter un utilisateur à un groupe

L’exemple suivant ajoute un utilisateur à un groupe.

Exemple de fichier *function.json*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'add'
    }))
```

#### <a name="remove-user-from-a-group"></a>Supprimer un utilisateur d’un groupe

L’exemple suivant supprime un utilisateur d’un groupe.

Exemple de fichier *function.json*

```json
{
    "type": "signalR",
    "name": "action",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "hubName": "chat",
    "direction": "out"
}
```

*\_\_init.py__*

```python
def main(req: func.HttpRequest, action: func.Out[str]) -> func.HttpResponse:
    action.set(json.dumps({
        'userId': 'userId1',
        'groupName': 'myGroup',
        'action': 'remove'
    }))
```

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

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
