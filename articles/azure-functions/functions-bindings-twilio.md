---
title: Liaison Twilio Azure Functions
description: Découvrez comment utiliser les liaisons Twilio dans Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 07/09/2018
ms.author: cshoe
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c24e8b6098ba33a2e738a7f5f310ae7e65ee516
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925289"
---
# <a name="twilio-binding-for-azure-functions"></a>Liaison Twilio pour Azure Functions

Cet article explique comment envoyer des SMS avec des liaisons [Twilio](https://www.twilio.com/) dans Azure Functions. Azure Functions prend en charge les liaisons de sortie pour Twilio.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Packages - Functions 1.x

Les liaisons Twilio sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio), version 1.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages - Functions 2.x et ultérieur

Les liaisons Twilio sont fournies dans le package NuGet [Microsoft.Azure.WebJobs.Extensions.Twilio](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Twilio), version 3.x. Le code source du package se trouve dans le référentiel GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example---functions-1x"></a>Exemple – Functions 1.x

Consultez l’exemple propre à un langage particulier :

* [C#](#c-example)
* [Script C# (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

### <a name="c-example"></a>Exemple en code C#

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui envoie un SMS quand elle est déclenchée par un message de file d’attente.

```cs
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX" )]
public static SMSMessage Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {order}");

    var message = new SMSMessage()
    {
        Body = $"Hello {order["name"]}, thanks for your order!",
        To = order["mobileNumber"].ToString()
    };

    return message;
}
```

Cet exemple utilise l’attribut `TwilioSms` avec la valeur de retour de la méthode. Une alternative consiste à utiliser l’attribut avec un paramètre `out SMSMessage` ou un paramètre `ICollector<SMSMessage>` ou `IAsyncCollector<SMSMessage>`.

### <a name="c-script-example"></a>Exemple de script C#

L’exemple suivant montre une liaison de sortie Twilio dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise un paramètre `out` pour envoyer un SMS.

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Voici le code du script C# :

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

Vous ne pouvez pas utiliser de paramètres de sortie dans du code asynchrone. Voici un exemple de code de script C# asynchrone :

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

### <a name="javascript-example"></a>Exemple JavaScript

L’exemple suivant montre une liaison de sortie Twilio dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="example---functions-2x-and-higher"></a>Exemple - Functions 2.x et ultérieur

Consultez l’exemple propre à un langage particulier :

* [C# 2.x+](#2x-c-example)
* [Script C# (.csx) 2.x+](#2x-c-script-example)
* [JavaScript 2.x+](#2x-javascript-example)

### <a name="2x-c-example"></a>Exemple C# 2.x+

L’exemple suivant montre une [fonction C#](functions-dotnet-class-library.md) qui envoie un SMS quand elle est déclenchée par un message de file d’attente.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;
namespace TwilioQueueOutput
{
    public static class QueueTwilio
    {
        [FunctionName("QueueTwilio")]
        [return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
        public static CreateMessageOptions Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order,
        ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed: {order}");

            var message = new CreateMessageOptions(new PhoneNumber(order["mobileNumber"].ToString()))
            {
                Body = $"Hello {order["name"]}, thanks for your order!"
            };

            return message;
        }
    }
}
```

Cet exemple utilise l’attribut `TwilioSms` avec la valeur de retour de la méthode. Une alternative consiste à utiliser l’attribut avec un paramètre `out CreateMessageOptions` ou un paramètre `ICollector<CreateMessageOptions>` ou `IAsyncCollector<CreateMessageOptions>`.

### <a name="2x-c-script-example"></a>Exemple de script C# 2.x+

L’exemple suivant montre une liaison de sortie Twilio dans un fichier *function.json* et une [fonction de script C#](functions-reference-csharp.md) qui utilise la liaison. La fonction utilise un paramètre `out` pour envoyer un SMS.

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Voici le code du script C# :

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static void Run(string myQueueItem, out CreateMessageOptions message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    message = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    message.Body = msg;
}
```

Vous ne pouvez pas utiliser de paramètres de sortie dans du code asynchrone. Voici un exemple de code de script C# asynchrone :

```cs
#r "Newtonsoft.Json"
#r "Twilio"
#r "Microsoft.Azure.WebJobs.Extensions.Twilio"

using System;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Microsoft.Azure.WebJobs.Extensions.Twilio;
using Twilio.Rest.Api.V2010.Account;
using Twilio.Types;

public static async Task Run(string myQueueItem, IAsyncCollector<CreateMessageOptions> message,  ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // You must initialize the CreateMessageOptions variable with the "To" phone number.
    CreateMessageOptions smsText = new CreateMessageOptions(new PhoneNumber("+1704XXXXXXX"));

    // A dynamic message can be set instead of the body in the output binding. In this example, we use
    // the order information to personalize a text message.
    smsText.Body = msg;

    await message.AddAsync(smsText);
}
```

### <a name="2x-javascript-example"></a>Exemple JavaScript 2.x+

L’exemple suivant montre une liaison de sortie Twilio dans un fichier *function.json* et une [fonction JavaScript](functions-reference-node.md) qui utilise la liaison.

Voici les données de liaison dans le fichier *function.json* :

Exemple de fichier function.json :

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSidSetting": "TwilioAccountSid",
  "authTokenSetting": "TwilioAuthToken",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```

Voici le code JavaScript :

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message in the binding, you must at least
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. The "To" number 
    // must be specified in code. 
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="attributes"></a>Attributs

Dans les [bibliothèques de classes C#](functions-dotnet-class-library.md), utilisez l’attribut [TwilioSms](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs).

Pour plus d’informations sur les propriétés d’attribut que vous pouvez configurer, consultez [Configuration](#configuration). Voici un exemple d’attribut `TwilioSms` dans une signature de méthode :

```csharp
[FunctionName("QueueTwilio")]
[return: TwilioSms(AccountSidSetting = "TwilioAccountSid", AuthTokenSetting = "TwilioAuthToken", From = "+1425XXXXXXX")]
public static CreateMessageOptions Run(
[QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] JObject order, ILogger log)
{
    ...
}
 ```

Vous trouverez un exemple complet sur la page [Exemple C#](#c-example).

## <a name="configuration"></a>Configuration

Le tableau suivant décrit les propriétés de configuration de liaison que vous définissez dans le fichier *function.json* et l’attribut `TwilioSms`.

| Propriété function.json v1 | Propriété function.json v2 | Propriété d’attribut |Description|
|---------|---------|---------|----------------------|
|**type**|**type**| Cette propriété doit être définie sur `twilioSms`.|
|**direction**|**direction**| Cette propriété doit être définie sur `out`.|
|**name**|**name**| Nom de la variable utilisée dans le code de fonction pour le SMS Twilio. |
|**accountSid**|**accountSidSetting**| **AccountSidSetting**| Cette valeur doit être définie sur le nom d’un paramètre d’application hébergeant le SID de votre compte Twilio, par ex., TwilioAccountSid. En l’absence de définition, le nom du paramètre d’application par défaut est « AzureWebJobsTwilioAccountSid ». |
|**authToken**|**authTokenSetting**|**AuthTokenSetting**| Cette valeur doit être définie sur le nom d’un paramètre d’application hébergeant le jeton d’authentification Twilio, par ex., TwilioAccountAuthToken. En l’absence de définition, le nom du paramètre d’application par défaut est « AzureWebJobsTwilioAuthToken ». |
|**to**| N/A : spécifier dans le code | **To**| Cette valeur est définie sur le numéro de téléphone sur lequel est envoyé le SMS.|
|**from**|**from** | **From**| Cette valeur est définie sur le numéro de téléphone à partir duquel est envoyé le SMS.|
|**body**|**body** | **Corps**| Cette valeur peut être utilisée pour coder en dur le SMS, si vous n’avez pas besoin de procéder à une définition dynamique dans le code associé à votre fonction. |  

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les déclencheurs et les liaisons Azure Functions](functions-triggers-bindings.md)
