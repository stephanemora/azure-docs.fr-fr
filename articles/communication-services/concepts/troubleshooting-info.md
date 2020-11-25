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
ms.openlocfilehash: 775075765c8c8eaa94541c0f094c1f7743fe59d9
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886785"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Résolution des problèmes dans Azure Communication Services

Ce document vous aide à résoudre les problèmes que vous pouvez rencontrer dans votre solution Communication Services. Si vous voulez résoudre des problèmes liés aux SMS, vous pouvez [activer la création de rapports de remise avec Event Grid](../quickstarts/telephony-sms/handle-sms-events.md) pour capturer les détails de remise de SMS.

## <a name="getting-help"></a>Obtenir de l’aide

Nous encourageons les développeurs à poser des questions, à suggérer des fonctionnalités et à signaler des problèmes dans le [dépôt GitHub](https://github.com/Azure/communication) Communication Services. Vous pouvez également consulter les forums suivants :

* [Microsoft Q&A](/answers/questions/topics/single/101418.html)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/azure+communication)

En fonction du [plan de support](https://azure.microsoft.com/support/plans/) de votre abonnement Azure, vous pouvoir avoir la possibilité d’envoyer un ticket de support directement par le biais du [portail Azure](https://azure.microsoft.com/support/create-ticket/).

Pour vous aider à résoudre certains types de problèmes, vous pouvez être invité à fournir les informations suivantes :

* **ID MS-CV** : cet ID sert à résoudre les problèmes liés aux appels et aux messages. 
* **ID d’appel** : cet ID sert à identifier les appels Communication Services.
* **ID de message SMS** : cet ID sert à identifier les messages SMS.
* **Journaux des appels** : ces journaux contiennent des informations détaillées qui peuvent être utilisés pour résoudre les problèmes d’appel et de réseau.


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

## <a name="enable-and-access-call-logs"></a>Activer et accéder aux journaux des appels




# <a name="javascript"></a>[JavaScript](#tab/javascript)

Le code suivant peut être utilisé pour configurer `AzureLogger`, afin de sortir les journaux dans la console à l’aide de la bibliothèque de client JavaScript :

```javascript
import { AzureLogger } from '@azure/logger'; 

AzureLogger.verbose = (...args) => { console.info(...args); } 
AzureLogger.info = (...args) => { console.info(...args); } 
AzureLogger.warning = (...args) => { console.info(...args); } 
AzureLogger.error = (...args) => { console.info(...args); } 

callClient = new CallClient({logger: AzureLogger}); 
```

# <a name="ios"></a>[iOS](#tab/ios)

Lors du développement pour iOS, vos journaux sont stockés dans des fichiers `.blog`. Notez que vous ne pouvez pas afficher les journaux directement, car ils sont chiffrés.

Vous pouvez y accéder en ouvrant Xcode. Accédez à Windows > Devices and Simulators (Appareils et simulateurs) > Appareils (Devices). Sélectionnez votre appareil. Sous Installed Apps (Applications installées), sélectionnez votre application, puis cliquez sur « Download container » (Télécharger le conteneur). 

Vous obtenez ainsi un fichier `xcappdata`. Cliquez avec le bouton droit sur ce fichier et sélectionnez « Afficher le contenu du package ». Vous voyez alors les fichiers `.blog` que vous pouvez ensuite joindre à votre demande de support Azure.

# <a name="android"></a>[Android](#tab/android)

Lors du développement pour Android, vos journaux sont stockés dans des fichiers `.blog`. Notez que vous ne pouvez pas afficher les journaux directement, car ils sont chiffrés.

Sur Android Studio, accédez à l’Explorateur de fichiers de l’appareil en sélectionnant View (Affichage) > Tool Windows (Fenêtres Outil) > Device File Explorer (Explorateur de fichiers de l’appareil) à partir du simulateur et de l’appareil. Le fichier `.blog` se trouve dans le répertoire de votre application, qui doit ressembler à ce qui suit `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`. Vous pouvez joindre ce fichier à votre demande de support. 
   

---


## <a name="related-information"></a>Informations connexes
- [Journaux et diagnostics](logging-and-diagnostics.md)
- [Métriques](metrics.md)