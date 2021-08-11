---
title: Résolution des problèmes dans Azure Communication Services
description: Découvrez comment rassembler les informations dont vous avez besoin pour dépanner votre solution Communication Services.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 66f2091087ed3602e929b584f7a311f4ebb05f88
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292514"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Résolution des problèmes dans Azure Communication Services

Ce document vous aide à résoudre les problèmes que vous pouvez rencontrer dans votre solution Communication Services. Si vous voulez résoudre des problèmes liés aux SMS, vous pouvez [activer la création de rapports de remise avec Event Grid](../quickstarts/telephony-sms/handle-sms-events.md) pour capturer les détails de remise de SMS.

## <a name="getting-help"></a>Obtenir de l’aide

Nous encourageons les développeurs à poser des questions, à suggérer des fonctionnalités et à signaler des problèmes. Pour cela, nous mettons à disposition une [page d’aide et de support dédiée](../support.md) qui liste les options de support.

Pour vous aider à résoudre certains types de problèmes, vous pouvez être invité à fournir les informations suivantes :

* **ID MS-CV** : cet ID sert à résoudre les problèmes liés aux appels et aux messages.
* **ID d’appel** : cet ID sert à identifier les appels Communication Services.
* **ID de message SMS** : cet ID sert à identifier les messages SMS.
* **Journaux des appels** : ces journaux contiennent des informations détaillées qui peuvent être utilisés pour résoudre les problèmes d’appel et de réseau.


## <a name="access-your-ms-cv-id"></a>Accéder à votre ID MS-CV

Vous pouvez accéder à l’ID MS-CV en configurant les diagnostics dans l’instance d’objet `clientOptions` lors de l’initialisation de vos kits SDK. Les diagnostics peuvent être configurés pour n’importe quel kit SDK Azure, notamment Conversation, Identité et Appel VoIP.

### <a name="client-options-example"></a>Exemple d’options client

Les extraits de code suivants illustrent la configuration des diagnostics. Lorsque les kits SDK sont utilisés avec les diagnostics activés, les détails des diagnostics sont émis sur l’écouteur d’événements configuré :

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

Lors de la résolution de problèmes d’appels vocaux ou vidéo, vous pouvez être invité à fournir un `call ID`. Vous pouvez y accéder via la propriété `id` de l’objet `call` :

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.startCall` or `callAgent.join` methods
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
// `call` is an instance of a call created by `callAgent.startCall(…)` or `callAgent.join(…)` methods
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

Le kit SDK Appel Azure communication Services s’appuie en interne sur la bibliothèque [@azure/logger](https://www.npmjs.com/package/@azure/logger) pour contrôler la journalisation.
Utilisez la méthode `setLogLevel` du package `@azure/logger` pour configurer la sortie de journal :

```javascript
import { setLogLevel } from '@azure/logger';
setLogLevel('verbose');
const callClient = new CallClient();
```

Vous pouvez utiliser AzureLogger pour rediriger la sortie de journalisation depuis les kits SDK Azure en remplaçant la méthode `AzureLogger.log`. Cette opération peut s’avérer utile si vous souhaitez rediriger les journaux vers un autre emplacement que celui de la console.

```javascript
import { AzureLogger } from '@azure/logger';
// redirect log output
AzureLogger.log = (...args) => {
  console.log(...args); // to console, file, buffer, REST API..
};
```

# <a name="ios"></a>[iOS](#tab/ios)

Lors du développement pour iOS, vos journaux sont stockés dans des fichiers `.blog`. Notez que vous ne pouvez pas afficher les journaux directement, car ils sont chiffrés.

Vous pouvez y accéder en ouvrant Xcode. Accédez à Windows > Devices and Simulators (Appareils et simulateurs) > Appareils (Devices). Sélectionnez votre appareil. Sous Installed Apps (Applications installées), sélectionnez votre application, puis cliquez sur « Download container » (Télécharger le conteneur).

Vous obtenez ainsi un fichier `xcappdata`. Cliquez avec le bouton droit sur ce fichier et sélectionnez « Afficher le contenu du package ». Vous voyez alors les fichiers `.blog` que vous pouvez ensuite joindre à votre demande de support Azure.

# <a name="android"></a>[Android](#tab/android)

Lors du développement pour Android, vos journaux sont stockés dans des fichiers `.blog`. Notez que vous ne pouvez pas afficher les journaux directement, car ils sont chiffrés.

Sur Android Studio, accédez à l’Explorateur de fichiers de l’appareil en sélectionnant View (Affichage) > Tool Windows (Fenêtres Outil) > Device File Explorer (Explorateur de fichiers de l’appareil) à partir du simulateur et de l’appareil. Le fichier `.blog` se trouve dans le répertoire de votre application, qui doit ressembler à ce qui suit `/data/data/[app_name_space:com.contoso.com.acsquickstartapp]/files/acs_sdk.blog`. Vous pouvez joindre ce fichier à votre demande de support.

---

## <a name="enable-and-access-call-logs-windows"></a>Activer et accéder aux journaux des appels (Windows)

Lors du développement pour Windows, vos journaux sont stockés dans des fichiers `.blog`. Notez que vous ne pouvez pas afficher les journaux directement, car ils sont chiffrés.

Vous pouvez y accéder en recherchant l’emplacement où votre application conserve ses données locales. Il existe de nombreuses façons de déterminer où une application UWP conserve ses données locales. Les étapes suivantes montrent l’une de ces façons :
1. Ouvrir une invite de commande Windows (touche Windows + R)
2. Saisissez `cmd.exe`
3. Saisissez `where /r %USERPROFILE%\AppData acs*.blog`
4. Veuillez vérifier si l’ID d’application de votre application correspond à celui renvoyé par la commande précédente.
5. Ouvrez le dossier contenant les fichiers journaux en saisissant `start ` suivi du chemin retourné par l’étape 3. Par exemple : `start C:\Users\myuser\AppData\Local\Packages\e84000dd-df04-4bbc-bf22-64b8351a9cd9_k2q8b5fxpmbf6`
6. Veuillez joindre tous les fichiers `*.blog` et `*.etl` à votre demande de support Azure.


## <a name="calling-sdk-error-codes"></a>Codes d’erreur du kit SDK Appel

Le kit SDK Appel Azure Communication Services utilise les codes d’erreur suivants pour vous aider à résoudre des problèmes d’appel. Ces codes d’erreur sont exposés via la propriété `call.callEndReason` à l’issue d’un appel.

| Code d'erreur | Description | Action à effectuer |
| -------- | ---------------| ---------------|
| 403 | Interdit / Échec de l’authentification. | Vérifiez que votre jeton Communication Services est valide et qu’il n’a pas expiré. Si vous utilisez l’interopérabilité Teams, assurez-vous que votre locataire Teams a été ajouté à la liste d’autorisation d’accès à la préversion. Pour activer/désactiver l’[interopérabilité des locataires Teams](./teams-interop.md), remplissez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).|
| 404 | Appel introuvable. | Assurez-vous que le numéro que vous appelez (ou l’appel que vous êtes en train de joindre) existe. |
| 408 | Le délai d’attente du contrôleur d’appel a expiré. | Le contrôleur d’appel a atteint le délai d’attente des messages de protocole provenant des points de terminaison utilisateur. Vérifiez que les clients sont connectés et disponibles. |
| 410 | Erreur locale de pile multimédia ou d’infrastructure multimédia. | Veillez à utiliser le kit SDK le plus récent dans un environnement pris en charge. |
| 430 | Impossible de remettre le message à l’application cliente. | Vérifiez que l’application cliente est en cours d’exécution et disponible. |
| 480 | Le point de terminaison de client distant n’est pas inscrit. | Vérifiez que le point de terminaison distant est disponible. |
| 481 | Échec du traitement de l’appel entrant. | Soumettez une demande de support via le portail Azure. |
| 487 | Appel annulé, refusé localement, terminé en raison d’un problème d’incompatibilité avec le point de terminaison, ou ne parvenant pas à générer l’offre multimédia. | Comportement attendu. |
| 490, 491, 496, 487, 498 | Problèmes de réseau de point de terminaison local. | Vérifiez votre réseau. |
| 500, 503, 504 | Erreur d’infrastructure Communication Services. | Soumettez une demande de support via le portail Azure. |
| 603 | Appel global refusé par le participant Communication Services distant | Comportement attendu. |

## <a name="chat-sdk-error-codes"></a>Codes d’erreur du Kit SDK Conversation

Le kit SDK Appel Azure Communication Conversation utilise les codes d’erreur suivants pour vous aider à résoudre des problèmes de conversation. Les codes d’erreur sont exposés via la propriété `error.code` dans la réponse d’erreur.

| Code d'erreur | Description | Action à effectuer |
| -------- | ---------------| ---------------|
| 401 | Non autorisé | Vérifiez que votre jeton Communication Services est valide et qu’il n’a pas expiré. |
| 403 | Interdit | Assurez-vous que l’initiateur de la demande a accès à la ressource. |
| 429 | Trop de demandes | Assurez-vous que votre application côté client gère ce scénario de manière conviviale. Si l’erreur persiste, envoyez une demande de support. |
| 503 | Service indisponible | Soumettez une demande de support via le portail Azure. |

## <a name="related-information"></a>Informations connexes
- [Journaux et diagnostics](logging-and-diagnostics.md)
- [Métriques](metrics.md)