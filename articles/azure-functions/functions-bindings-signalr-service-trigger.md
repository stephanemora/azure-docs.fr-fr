---
title: Liaison de déclencheur SignalR Service pour Azure Functions
description: Apprenez à envoyer des messages SignalR Service à partir d’Azure Functions.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763509"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Liaison de déclencheur SignalR Service pour Azure Functions

Utilisez la liaison de déclencheur *SignalR* pour répondre aux messages envoyés par Azure SignalR Service. Quand la fonction est déclenchée, les messages passés à la fonction sont analysés en tant qu’objets JSON.

Dans le mode sans serveur du service SignaIR, le service SignaIR utilise la fonctionnalité en [amont](../azure-signalr/concept-upstream.md) pour envoyer des messages du client vers Function App. Et Function App utilise la liaison de déclencheur de service SignalR pour gérer ces messages. L’architecture générale est illustrée ci-dessous : :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="Architecture du déclencheur signalR":::

Pour plus d’informations sur les détails d’installation et de configuration, consultez la [vue d’ensemble](functions-bindings-signalr-service.md).

## <a name="example"></a>Exemple

L’exemple suivant montre une fonction qui reçoit un message par la liaison de déclencheur et journalise le message.

# <a name="c"></a>[C#](#tab/csharp)

La liaison de déclencheur SignalR Service pour C# a deux modèles de programmation : un modèle basé sur la classe et un modèle traditionnel. Le modèle basé sur la classe peut fournir une expérience de programmation cohérente côté serveur SignalR. Le modèle traditionnel offre une plus grande souplesse et présente des similitudes avec d’autres liaisons de fonction.

### <a name="with-class-based-model"></a>Avec le modèle basé sur la classe

Pour plus d’informations, consultez [Modèle basé sur la classe](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model).

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>Avec le modèle traditionnel

Le modèle traditionnel obéit à la convention d’Azure Function développée par C#. Si vous n’êtes pas familiarisé avec ce langage, vous pouvez vous reporter à ces [documents](./functions-dotnet-class-library.md).

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>Utiliser l’attribut `[SignalRParameter]` pour simplifier `ParameterNames`

À la place de `ParameterNames`, qui est un peu lourd à manier, utilisez `SignalRParameter` qui permet d’obtenir le même résultat.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[Script C#](#tab/csharp-script)

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Voici le code de script C# :

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

Voici le code Python :

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>Configuration

### <a name="signalrtrigger"></a>SignalRTrigger

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `SignalRTrigger`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**| n/a | Cette propriété doit être définie sur `SignalRTrigger`.|
|**direction**| n/a | Cette propriété doit être définie sur `in`.|
|**name**| n/a | Nom de variable utilisé dans le code de fonction pour le contexte d’appel du déclencheur. |
|**hubName**|**HubName**| Cette valeur doit être définie sur le nom du hub SignalR pour que la fonction soit déclenchée.|
|**category**|**Catégorie**| Cette valeur doit être définie sur la catégorie des messages pour que la fonction soit déclenchée. La catégorie peut avoir l’une des valeurs suivantes : <ul><li>**connections** : pour les événements *connected* et *disconnected*</li><li>**messages** : pour tous les autres événements, hormis ceux de la catégorie *connections*</li></ul> |
|**event**|**Event**| Cette valeur doit être définie sur l’événement des messages pour que la fonction soit déclenchée. Pour la catégorie *messages*, l’événement est *target* dans les [messages d’appel](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) que les clients envoient. Pour la catégorie *connections*, seuls les événements *connected* et *disconnected* sont utilisés. |
|**parameterNames**|**ParameterNames**| (Facultatif) Liste des noms liés aux paramètres. |
|**connectionStringSetting**|**ConnectionStringSetting**| Nom du paramètre d’application contenant la chaîne de connexion du service SignalR (« AzureSignalRConnectionString » par défaut) |

## <a name="payload"></a>Payload

Le type d’entrée du déclencheur est déclaré comme étant `InvocationContext` ou un type personnalisé. Si vous choisissez `InvocationContext`, vous obtenez un accès complet au contenu de la requête. Pour un type personnalisé, le runtime tente d’analyser le corps de la requête JSON pour définir les propriétés de l’objet.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext comporte tout le contenu du message envoyé par SignalR Service.

|Propriété dans InvocationContext | Description|
|------------------------------|------------|
|Arguments| Disponible pour la catégorie *messages*. Contient des *arguments* dans le [message d’appel](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)|
|Error| Disponible pour l’événement *disconnected*. Elle peut être vide si la connexion s’est fermée sans erreur ou si elle contient les messages d’erreur.|
|Hub| Nom du hub auquel le message appartient.|
|Category| Catégorie du message.|
|Événement| Événement du message.|
|ConnectionId| ID de connexion du client qui envoie le message.|
|UserId| Identité de l’utilisateur du client qui envoie le message.|
|headers| en-têtes de la requête.|
|Requête| Requête de la demande lorsque les clients se connectent au service.|
|Revendications| Revendications du client.|

## <a name="using-parameternames"></a>Utilisation de `ParameterNames`

La propriété `ParameterNames` dans `SignalRTrigger` vous permet de lier les arguments des messages d’appel aux paramètres des fonctions. Le nom que vous avez défini peut être utilisé dans le cadre des [expressions de liaison](../azure-functions/functions-bindings-expressions-patterns.md) dans d’autres liaisons ou en tant que paramètres dans votre code. C’est un moyen plus pratique d’accéder aux arguments de `InvocationContext`.

Imaginons que vous ayez un client JavaScript SignalR qui tente d’appeler la méthode `broadcast` dans Azure Function avec deux arguments `message1`, `message2`.

```javascript
await connection.invoke("broadcast", message1, message2);
```

Une fois que vous avez défini `parameterNames`, le nom que vous avez défini correspond respectivement aux arguments envoyés côté client. 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

Ensuite, le `arg1` contiendra le contenu de `message1` et `arg2` contiendra le contenu de `message2`.


### <a name="remarks"></a>Notes

L’ordre de la liaison de paramètre est important. Si vous utilisez `ParameterNames`, l’ordre dans `ParameterNames` correspond à l’ordre des arguments que vous appelez dans le client. Si vous utilisez l’attribut `[SignalRParameter]` dans C#, l’ordre des arguments dans les méthodes Azure Function correspond à l’ordre des arguments dans les clients.

`ParameterNames` et l’attribut `[SignalRParameter]` **ne peuvent pas** être utilisés simultanément ; sinon, une exception sera générée.

## <a name="signalr-service-integration"></a>Intégration du service signalR

Le service signalR a besoin d’une URL pour accéder à Function App lorsque vous utilisez la liaison de déclencheur de service SignalR. L’URL doit être configurée dans **paramètres en amont** du côté service SignalR. 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="Portail en amont":::

Lorsque vous utilisez le déclencheur de service SignalR, l’URL peut être simple et mise en forme comme indiqué ci-dessous :

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

Le `Function_App_URL` se trouve sur la page Vue d’ensemble de Function App et la `API_KEY` est générée par Azure Function. Vous pouvez récupérer `API_KEY` à partir de `signalr_extension` dans le panneau **Clés d’application** de Function App.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="Clé API":::

Si vous souhaitez utiliser plusieurs Function App avec un service SignalR, les règles de routage complexes peuvent être prises en charge en amont. Pour plus d’informations, consultez [Paramètres en amont](../azure-signalr/concept-upstream.md).

## <a name="step-by-step-sample"></a>Exemple de procédure pas à pas

Vous pouvez suivre l’exemple dans GitHub pour déployer une salle de conversation sur Function App avec la liaison de déclencheur du service SignalR et la fonctionnalité en amont : [Exemple de salle de conversation bidirectionnelle](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)

## <a name="next-steps"></a>Étapes suivantes

* [Développement et configuration Azure Functions avec Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)
* [Exemple de liaison de déclencheur SignalR Service](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
