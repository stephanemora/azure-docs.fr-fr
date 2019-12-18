---
title: Liaisons SendGrid dans Azure Functions
description: Informations de référence sur les liaisons SendGrid dans Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: cshoe
ms.openlocfilehash: a96cd537328a1a9edeeb03f81350ed5393806765
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927578"
---
# <a name="azure-functions-sendgrid-bindings"></a>Liaisons SendGrid dans Azure Functions

Cet article explique comment envoyer des e-mails avec des liaisons [SendGrid](https://sendgrid.com/docs/User_Guide/index.html) dans Azure Functions. Azure Functions prend en charge une liaison de sortie pour SendGrid.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Les liaisons SendGrid sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), version 2.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.SendGrid/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages – Functions 2.x et versions ultérieures

Les liaisons SendGrid sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.SendGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SendGrid), version 3.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/).

> [!NOTE]
> Les versions 2.x et ultérieures ne créent pas la rubrique ni l’abonnement configurés dans l’instance `ServiceBusTrigger`. Ces versions sont basées sur [Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus), qui ne gère pas les files d’attente.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Exemples

Consultez l’exemple propre à un langage particulier :

* [C#](#c-example)
* [Script C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#java-example)

### <a name="c-example"></a>Exemple en code C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui utilise un déclencheur de file d’attente Service Bus et une liaison de sortie SendGrid.

#### <a name="synchronous-c-example"></a>Exemple C# synchrone :

```cs
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

message = new SendGridMessage();
message.AddTo(emailObject.To);
message.AddContent("text/html", emailObject.Body);
message.SetFrom(new EmailAddress(emailObject.From));
message.SetSubject(emailObject.Subject);
}

public class OutgoingEmail
{
    public string To { get; set; }
    public string From { get; set; }
    public string Subject { get; set; }
    public string Body { get; set; }
}
```
#### <a name="asynchronous-c-example"></a>Exemple C# asynchrone :

```cs
[FunctionName("SendEmail")]
public static async void Run(
 [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] Message email,
 [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] IAsyncCollector<SendGridMessage> messageCollector)
{
 var emailObject = JsonConvert.DeserializeObject<OutgoingEmail>(Encoding.UTF8.GetString(email.Body));

 var message = new SendGridMessage();
 message.AddTo(emailObject.To);
 message.AddContent("text/html", emailObject.Body);
 message.SetFrom(new EmailAddress(emailObject.From));
 message.SetSubject(emailObject.Subject);
 
 await messageCollector.AddAsync(message);
}

public class OutgoingEmail
{
 public string To { get; set; }
 public string From { get; set; }
 public string Subject { get; set; }
 public string Body { get; set; }
}
```

Vous pouvez omettre la définition de la propriété `ApiKey` de l’attribut si votre clé API se trouve dans un paramètre d’application nommé « AzureWebJobsSendGridApiKey ».

### <a name="c-script-example"></a>Exemple de script C#

L’exemple suivant montre une liaison de sortie SendGrid dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

```json 
{
    "bindings": [
        {
          "type": "queueTrigger",
          "name": "mymsg",
          "queueName": "myqueue",
          "connection": "AzureWebJobsStorage",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "$return",
          "direction": "out",
          "apiKey": "SendGridAPIKeyAsAppSetting",
          "from": "{FromEmail}",
          "to": "{ToEmail}"
        }
    ]
}
```

La section [configuration](#configuration) décrit ces propriétés.

Voici le code Script C# :

```csharp
#r "SendGrid"

using System;
using SendGrid.Helpers.Mail;
using Microsoft.Azure.WebJobs.Host;

public static SendGridMessage Run(Message mymsg, ILogger log)
{
    SendGridMessage message = new SendGridMessage()
    {
        Subject = $"{mymsg.Subject}"
    };
    
    message.AddContent("text/plain", $"{mymsg.Content}");

    return message;
}
public class Message
{
    public string ToEmail { get; set; }
    public string FromEmail { get; set; }
    public string Subject { get; set; }
    public string Content { get; set; }
}
```

### <a name="java-example"></a>Exemple Java

L’exemple suivant utilise l’annotation `@SendGridOutput` de la [bibliothèque du runtime des fonctions Java](/java/api/overview/azure/functions/runtime) pour envoyer un e-mail à l’aide de la liaison de sortie SendGrid.

```java
@FunctionName("SendEmail")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @SendGridOutput(
                name = "email", dataType = "String", apiKey = "SendGridConnection", to = "test@example.com", from = "test@example.com",
                subject= "Sending with SendGrid", text = "Hello from Azure Functions"
                ) OutputBinding<String> email
            )
    {
        String name = request.getBody().orElse("World");

        final String emailBody = "{\"personalizations\":" +
                                    "[{\"to\":[{\"email\":\"test@example.com\"}]," +
                                    "\"subject\":\"Sending with SendGrid\"}]," +
                                    "\"from\":{\"email\":\"test@example.com\"}," +
                                    "\"content\":[{\"type\":\"text/plain\",\"value\": \"Hello" + name + "\"}]}";

        email.setValue(emailBody);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
    }
```

### <a name="javascript-example"></a>Exemple JavaScript

L’exemple suivant montre une liaison de sortie SendGrid dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

La section [configuration](#configuration) décrit ces propriétés.

Voici le code JavaScript :

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};
```

## <a name="attributes"></a>Attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [SendGrid](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.SendGrid/SendGridAttribute.cs).

Pour plus d’informations sur les propriétés d’attribut que vous pouvez configurer, consultez [Configuration](#configuration). Voici un exemple d’attribut `SendGrid` dans une signature de méthode :

```csharp
[FunctionName("SendEmail")]
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] OutgoingEmail email,
    [SendGrid(ApiKey = "CustomSendGridKeyAppSettingName")] out SendGridMessage message)
{
    ...
}
```

Vous trouverez un exemple complet sur la page [Exemple C#](#c-example).

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `SendGrid`.

|Propriété function.json | Propriété d’attribut |Description|
|---------|---------|----------------------|
|**type**|| Obligatoire : doit être défini sur `sendGrid`.|
|**direction**|| Obligatoire : doit être défini sur `out`.|
|**name**|| Obligatoire : nom de variable utilisé dans le code de la fonction pour la requête ou le corps de la requête. Cette valeur est ```$return``` lorsqu’il n’existe qu’une valeur de retour. |
|**apiKey**|**ApiKey**| Nom d’un paramètre d’application qui contient votre clé API. En l’absence de définition, le nom du paramètre d’application par défaut est « AzureWebJobsSendGridApiKey ».|
|**to**|**To**| Adresse e-mail du destinataire. |
|**from**|**From**| Adresse e-mail de l’expéditeur. |
|**subject**|**Subject**| Objet de l’e-mail. |
|**text**|**Text**| Contenu de l’e-mail. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Paramètres host.json

Cette section décrit les paramètres de configuration globale disponibles pour cette liaison dans les versions 2.x et ultérieures. L’exemple de fichier host.json ci-dessous contient seulement les paramètres des version 2.x et ultérieures pour cette liaison. Pour plus d’informations sur les paramètres de configuration globale dans les version 2.x et ultérieures, consultez [Informations de référence sur le fichier host.json pour Azure Functions](functions-host-json.md).

> [!NOTE]
> Pour obtenir des informations de référence sur le fichier host.json dans Functions 1.x, consultez [Informations de référence sur le fichier host.json pour Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "sendGrid": {
            "from": "Azure Functions <samples@functions.com>"
        }
    }
}
```  

|Propriété  |Default | Description |
|---------|---------|---------| 
|from|n/a|Adresse e-mail de l’expéditeur dans toutes les fonctions.| 


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
