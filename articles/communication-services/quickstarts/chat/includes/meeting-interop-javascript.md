---
title: Démarrage rapide - Participer à une réunion Teams
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 2fb04acd75d607772b6582882b98f9ed222f070c
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111429574"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment rejoindre une conversation dans une réunion Teams à l’aide du kit SDK Conversation Azure Communication Services pour JavaScript.

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/join-chat-to-teams-meeting).

## <a name="prerequisites"></a>Prérequis 

*  [Déploiement Teams](/deployoffice/teams-install). 
* [Application de conversation](../get-started.md) opérationnelle. 

## <a name="enable-teams-interoperability"></a>Activer l’interopérabilité de Teams 

Un utilisateur Communication Services qui rejoint une réunion Teams en tant qu’utilisateur invité ne peut accéder à la conversation de la réunion qu’après avoir rejoint l’appel de réunion Teams. Consultez la documentation [Interopérabilité de Teams](../../voice-video-calling/get-started-teams-interop.md) pour savoir comment ajouter un utilisateur Communication Services à un appel de réunion Teams.

Vous devez être membre de l’organisation propriétaire des deux entités pour pouvoir utiliser cette fonctionnalité.

## <a name="joining-the-meeting-chat"></a>Participation à la conversation d’une réunion 

Une fois l’interopérabilité de Teams activée, un utilisateur de Communication Services peut rejoindre l’appel Teams en tant qu’utilisateur externe à l’aide du kit de développement logiciel (SDK) Calling. En rejoignant l’appel, il est également ajouté comme participant à la conversation de la réunion, où il peut échanger des messages avec d’autres utilisateurs lors de l’appel. L’utilisateur n’aura pas accès aux messages de conversation qui ont été envoyés avant qu’il ne rejoigne l’appel. Pour participer à la réunion et commencer à converser, vous pouvez suivre les étapes suivantes.

## <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir chat-interop-quickstart && cd chat-interop-quickstart
```

Exécutez `npm init -y` pour créer un fichier **package.json** avec les paramètres par défaut.

```console
npm init -y
```

## <a name="install-the-chat-packages"></a>Installer les packages de chat

Utilisez la commande `npm install` pour installer les kits de développement logiciel (SDK) Communication Services pour JavaScript nécessaires.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

L’option `--save` liste la bibliothèque comme dépendance dans votre fichier **package.json**.

## <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ce guide de démarrage rapide utilise webpack pour regrouper les ressources de l’application. Exécutez la commande suivante pour installer les packages npm webpack, webpack-cli et webpack-dev-server, puis listez-les comme dépendances de développement dans votre fichier **package.json** :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Créez un fichier **index.html** dans le répertoire racine de votre projet. Nous allons utiliser ce fichier pour configurer une disposition de base qui permettra à l’utilisateur de participer à une réunion et de démarrer une conversation.

## <a name="add-the-teams-ui-controls"></a>Ajouter les contrôles d’interface utilisateur de Teams

Remplacez le code dans index.html par l’extrait de code suivant.
Les zones de texte en haut de la page sont utilisées pour entrer le contexte de réunion et l’ID de thread de réunion de l’équipe. Le bouton « Participer à la réunion Teams » sera utilisé pour rejoindre la réunion spécifiée.
Une fenêtre de conversation contextuelle s’affiche au bas de la page. Elle permet d’envoyer des messages sur le thread de la réunion et affiche en temps réel tous les messages envoyés sur ce thread tant que l’utilisateur Communication Services en est membre.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Activer les contrôles d’interface utilisateur de Teams

Remplacez le contenu du fichier client.js par l’extrait de code suivant.

Dans l’extrait de code, remplacez : 
- `SECRET_CONNECTION_STRING` par la chaîne de connexion de votre service de communication ; 
- `ENDPOINT_URL` par l’URL du point de terminaison de votre service de communication.

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
    const connectionString = "<SECRET_CONNECTION_STRING>";
    const endpointUrl = "<ENDPOINT_URL>";

    const identityClient = new CommunicationIdentityClient(connectionString);

    let identityResponse = await identityClient.createUser();
    userId = identityResponse.communicationUserId;
    console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);

    let tokenResponse = await identityClient.getToken(identityResponse, [
        "voip",
        "chat",
    ]);

    const { token, expiresOn } = tokenResponse;
    console.log(`\nIssued an access token that expires at: ${expiresOn}`);
    console.log(token);

    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential(token);
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;

    chatClient = new ChatClient(
        endpointUrl,
        new AzureCommunicationTokenCredential(token)
    );

    console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});

    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button and chat box states
    chatBox.style.display = "block";
    hangUpButton.disabled = false;
    callButton.disabled = true;

    messagesContainer.innerHTML = messages;

    console.log(call);

    // open notifications channel
    await chatClient.startRealtimeNotifications();

    // subscribe to new message notifications
    chatClient.on("chatMessageReceived", (e) => {
        console.log("Notification chatMessageReceived!");

      // check whether the notification is intended for the current thread
        if (threadIdInput.value != e.threadId) {
            return;
        }

        if (e.sender.communicationUserId != userId) {
           renderReceivedMessage(e.message);
        }
        else {
           renderSentMessage(e.message);
        }
    });

    chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
    messages += '<div class="container lighter">' + message + '</div>';
    messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
    messages += '<div class="container darker">' + message + '</div>';
    messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
    {
        // end the current call
        await call.hangUp();

        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        callStateElement.innerText = '-';

        // toggle chat states
        chatBox.style.display = "none";
        messages = "";
    });

sendMessageButton.addEventListener("click", async () =>
    {
        let message = messagebox.value;

        let sendMessageRequest = { content: message };
        let sendMessageOptions = { senderDisplayName : 'Jack' };
        let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
        let messageId = sendChatMessageResult.id;

        messagebox.value = '';
        console.log(`Message sent!, message id:${messageId}`);
    });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obtenir le fil de conversation d’une réunion Teams pour un utilisateur Communication Services

Il est possible récupérer le lien et la conversation de la réunion Teams à l’aide d’API Graph détaillées dans la [Documentation Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true). Le kit SDK Communication Services Calling accepte un lien de réunion Teams complet. Ce lien est renvoyé avec la ressource `onlineMeeting`, accessible sous la propriété [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Avec les [API Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true), vous pouvez également obtenir le `threadId`. La réponse aura un objet `chatInfo` contenant le `threadID`. 

Vous pouvez également récupérer les informations de réunion et l’ID de thread requis à partir de l’URL **Rejoindre la réunion** disponible dans l’invitation à la réunion Teams proprement dite.
Un lien de réunion Teams ressemble à ceci : `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Le `threadId` sera là où `meeting_chat_thread_id` se trouve dans le lien. Assurez-vous que le `meeting_chat_thread_id` n’est pas échappé avant de l’utiliser. Il doit être au format suivant : `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Exécuter le code

Les utilisateurs de Webpack peuvent utiliser `webpack-dev-server` pour générer et exécuter votre application. Exécutez la commande suivante pour créer un bundle de votre application hôte sur un serveur web local :

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Ouvrez votre navigateur et accédez à http://localhost:8080/. Les éléments suivants doivent s’afficher :

:::image type="content" source="../join-teams-meeting-chat-quickstart.png" alt-text="Capture d’écran de l’application JavaScript terminée.":::

Insérez le lien de réunion et l’ID de thread Teams dans les zones de texte. Appuyez sur *Participer à une réunion Teams* pour participer à la réunion Teams. Une fois que l’utilisateur Communication Services est admis à la réunion, vous pouvez converser à partir de votre application Azure Communication Services. Accédez à la zone en bas de la page pour commencer la conversation.

> [!NOTE] 
> Actuellement, seuls l’envoi, la réception et la modification de messages sont pris en charge pour les scénarios d’interopérabilité avec Teams. D’autres fonctionnalités, telles que les indicateurs de saisie et les utilisateurs Communication Services qui ajoutent ou suppriment d’autres utilisateurs dans la réunion Teams, ne sont pas prises en charge pour l’instant.
