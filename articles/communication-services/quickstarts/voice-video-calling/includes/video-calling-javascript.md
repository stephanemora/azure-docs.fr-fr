---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 8b0992fb189d7abef276318b94ddd56c198d7f04
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109483224"
---
Prenez en main Azure Communication Services en utilisant le SDK d’appel Communication Services pour ajouter des appels vidéo 1 à 1 à votre application. Vous allez découvrir comment démarrer un appel vidéo et y répondre à l’aide du SDK d’appel Azure Communication Services pour JavaScript.


> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).


## <a name="prerequisites"></a>Prérequis
- Obtenez un compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/), Active LTS et Maintenance LTS (version 8.11.1 et 10.14.1).
- Créez une ressource Communication Services active. [Créez une ressource Communication Services](../../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Créez un jeton d’accès utilisateur pour instancier le client d’appel. [Découvrez comment créer et gérer des jetons d’accès utilisateur](../../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Configuration
### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js
Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Installer le package
Utilisez la commande `npm install` pour installer le SDK Azure Communication Services Calling pour JavaScript.

> [!IMPORTANT]
> Ce guide de démarrage rapide utilise la version `1.0.0.beta-10` du SDK Azure Communication Services Calling. 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ce guide de démarrage rapide utilise webpack pour regrouper les ressources de l’application. Exécutez la commande suivante pour installer les packages npm `webpack`, `webpack-cli` et `webpack-dev-server`, puis listez-les comme dépendances de développement dans votre fichier `package.json` :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Créez un fichier `index.html` dans le répertoire racine de votre projet. Nous utiliserons ce fichier pour configurer une disposition de base qui permettra à l’utilisateur d’effectuer un appel vidéo 1 à 1.

Voici le code :
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Créez un fichier dans le répertoire racine de votre projet sous le nom `client.js` qui contiendra la logique d’application pour ce guide de démarrage rapide. Ajoutez le code suivant pour importer le client appelant et obtenir des références aux éléments DOM.

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom      | Description | 
| :---        |    :----   |
| CallClient  | CallClient est le point d’entrée principal du SDK Calling.      |
| CallAgent  | CallAgent sert à démarrer et à gérer les appels.        |
| DeviceManager | DeviceManager est utilisé pour gérer les appareils multimédias.    |
| AzureCommunicationTokenCredential | La classe AzureCommunicationTokenCredential implémente l’interface CommunicationTokenCredential qui est utilisée pour instancier CallAgent.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Authentifier le client et accéder à DeviceManager

Vous devez remplacer <USER_ACCESS_TOKEN> par un jeton d’accès utilisateur valide pour votre ressource. Consultez la documentation sur les jetons d’accès utilisateur si vous n’avez pas encore de jeton disponible. À l’aide du `CallClient`, initialisez une instance de `CallAgent` avec un `CommunicationUserCredential` qui nous permettra d’établir et de recevoir des appels. Pour accéder à `DeviceManager`, une instance callAgent doit d’abord être créée. Vous pouvez ensuite utiliser la méthode `getDeviceManager` sur l’instance `CallClient` pour obtenir `DeviceManager`.

Ajoutez le code suivant à `client.js` :

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Passer un appel vidéo sortant 1 à 1 à un utilisateur

Ajoutez un écouteur d’événements pour initier un appel en cas de clic sur `callButton` :

Vous devez d’abord énumérer des caméras locales à l’aide de l’API `getCameraList` de DeviceManager. Dans ce démarrage rapide, nous utilisons la première caméra de la collection. Une fois que la caméra souhaitée est sélectionnée, une instance de LocalVideoStream est construite et transmise dans `videoOptions` comme élément du tableau localVideoStream à la méthode d’appel. Une fois l’appel connecté, il commencera automatiquement à envoyer un flux vidéo à l’autre participant. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Pour restituer un `LocalVideoStream`, vous devez créer une nouvelle instance de `VideoStreamRenderer`, puis créer une nouvelle instance de `VideoStreamRendererView` à l’aide de la méthode `createView` asynchrone. Vous pouvez ensuite attacher `view.target` à un élément d’interface utilisateur quelconque. 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Tous les participants distants sont disponibles via la collection `remoteParticipants` d’une instance d’appel. Vous devez écouter l’événement `remoteParticipantsUpdated` pour être averti lorsqu’un nouveau participant distant est ajouté à l’appel. Vous devez également parcourir la collection `remoteParticipants` pour vous abonner à chacun d’eux ainsi qu’à leurs flux vidéo. 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
Vous devez vous abonner à l’événement `videoStreamsUpdated` pour gérer les flux vidéo ajoutés des participants distants. Vous pouvez inspecter les collections `videoStreams` pour lister les flux de chaque participant tout en parcourant la collection `remoteParticipants` de l’appel en cours.

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
Vous devez vous abonner à un événement `isAvailableChanged` pour afficher `remoteVideoStream`. Si la propriété `isAvailable` prend la valeur `true`, un participant distant envoie un flux. Chaque fois que la disponibilité d’un flux distant change, vous pouvez choisir de détruire l’intégralité de `Renderer`, un `RendererView` spécifique, ou de les conserver, mais cela entraîne l’affichage d’une image vidéo vide.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Pour restituer un `RemoteVideoStream`, vous devez créer une nouvelle instance de `VideoStreamRenderer`, puis créer une nouvelle instance de `VideoStreamRendererView` à l’aide de la méthode `createView` asynchrone. Vous pouvez ensuite attacher `view.target` à un élément d’interface utilisateur quelconque. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Recevoir un appel entrant
Pour gérer les appels entrants, vous devez écouter l’événement `incomingCall` de `callAgent`. Une fois qu’il y a un appel entrant, vous devez énumérer les caméras locales et construire une instance de `LocalVideoStream` pour envoyer un flux vidéo à l’autre participant. Vous devez également vous abonner à `remoteParticipants` pour gérer les flux vidéo distants. Vous pouvez accepter ou rejeter l’appel par le biais de l’instance de `incomingCall`. 

Placez l’implémentation dans `init()` pour gérer les appels entrants. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);  
});
```
## <a name="end-the-current-call"></a>Terminer l’appel en cours
Ajoutez un détecteur d’événements pour terminer l’appel en cours en cas de clic sur `hangUpButton` :
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>S’abonner aux mises à jour d’appels
Vous devez vous abonner à l’événement lorsque le participant distant met fin à l’appel pour supprimer les renderers vidéo et les états des boutons bascules. 

Placez l’implémentation dans init() pour vous abonner à l’événement `callsUpdated`. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Démarrer et arrêter la vidéo pendant l’appel
Vous pouvez arrêter la vidéo pendant l’appel en cours en ajoutant un écouteur d’événements au bouton Arrêter la vidéo pour supprimer le renderer de `localVideoStream`. 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
Vous pouvez ajouter un écouteur d’événements au bouton Démarrer la vidéo pour réactiver la vidéo lorsqu’elle a été arrêtée pendant l’appel en cours. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>Exécuter le code
Utilisez `webpack-dev-server` pour créer et exécuter votre application. Exécutez la commande suivante pour créer un bundle de l’application hôte sur un serveur web local :

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Ouvrez votre navigateur et accédez à http://localhost:8080/. Les éléments suivants doivent s’afficher :

:::image type="content" source="../media/javascript/1-on-1-video-calling.png" alt-text="Page d’appel vidéo 1 à 1":::

Vous pouvez effectuer un appel vidéo sortant 1 à 1 en fournissant un identifiant utilisateur dans le champ de texte et en cliquant sur le bouton Start Call. 

## <a name="sample-code"></a>Exemple de code
Vous pouvez télécharger l’exemple d’application sur [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).