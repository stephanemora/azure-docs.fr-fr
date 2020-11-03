---
title: Résolution des problèmes dans Azure Communication Services
description: Découvrez comment rassembler les informations dont vous avez besoin pour dépanner votre solution Communication Services.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754668"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Résolution des problèmes dans Azure Communication Services

Ce document va vous aider à rassembler les informations dont vous avez besoin pour dépanner votre solution Communication Services.

## <a name="getting-help"></a>Obtenir de l’aide

Nous encourageons les développeurs à poser des questions, à suggérer des fonctionnalités et à signaler des problèmes dans le [dépôt GitHub](https://github.com/Azure/communication) Communication Services. Vous pouvez également consulter les forums suivants :

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/azure+communication)

En fonction du [plan de support](https://azure.microsoft.com/support/plans/) de votre abonnement Azure, vous pouvoir avoir la possibilité d’envoyer un ticket de support directement par le biais du [portail Azure](https://azure.microsoft.com/support/create-ticket/).

Pour vous aider à résoudre certains types de problèmes, vous pouvez être invité à fournir les informations suivantes :

* **ID MS-CV**  : cet ID sert à résoudre les problèmes liés aux appels et aux messages. 
* **ID d’appel**  : cet ID sert à identifier les appels Communication Services.
* **ID de message SMS**  : cet ID sert à identifier les messages SMS.

## <a name="access-your-ms-cv-id"></a>Accéder à votre ID MS-CV

Vous pouvez accéder à l’ID MS-CV en configurant les diagnostics dans l’instance d’objet `clientOptions` lors de l’initialisation de vos bibliothèques de client. Les diagnostics peuvent être configurés pour n’importe quelle bibliothèque de client Azure, notamment Conversation, Administration et Appel VoIP.

### <a name="client-options-example"></a>Exemple d’options client

Les extraits de code suivants illustrent la configuration des diagnostics. Lorsque les bibliothèques de client sont utilisées avec les diagnostics activés, les détails des diagnostics sont émis sur l’écouteur d’événements configuré :

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>Accéder à votre ID d’appel

Lors de l’envoi d’une demande de support liée à des problèmes d’appel par le biais du portail Azure, vous pouvez être invité à fournir l’ID de l’appel auquel vous faites référence. Vous pouvez y accéder par le biais de la bibliothèque de client appelante :

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>Accéder à votre ID de message SMS

Pour les problèmes liés aux SMS, vous pouvez recueillir l’ID de message à partir de l’objet de réponse.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Informations connexes
- [Journaux et diagnostics](logging-and-diagnostics.md)
- [Métriques](metrics.md)
