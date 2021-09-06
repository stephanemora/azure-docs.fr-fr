---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: mikben
ms.openlocfilehash: ead0e2f9e04c41ec729985f7c524874910aa9dc4
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114339461"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../../create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../../access-tokens.md).
- Facultatif : suivez le guide de démarrage rapide permettant d’[ajouter l’appel vocal à votre application](../../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Installer le SDK

Utilisez la commande `npm install` pour installer les kits SDK Appel Azure Communication Services et communs pour JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
Le SDK d’appel web de Communication Services doit être utilisé via `https`. Pour un développement local, utilisez `localhost` ou « fichier : » local.

## <a name="documentation-support"></a>Support par documentation
- [Signaler des problèmes/bogues sur GitHub](https://github.com/Azure/Communication/issues)
- [Exemples d’applications](../../../../samples/overview.md)
- [Référence sur l’API](/javascript/api/azure-communication-services/@azure/communication-calling/?preserve-view=true&view=azure-communication-services-js)

## <a name="models"></a>Modèles
### <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom                                | Description                                                                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                        | Point d’entrée principal du SDK Appel.                                                                                                 |
| `AzureCommunicationTokenCredential` | Implémente l’interface `CommunicationTokenCredential`, qui est utilisée pour instancier `callAgent`.                                       |
| `CallAgent`                         | Sert à démarrer et à gérer les appels.                                                                                                          |
| `DeviceManager`                     | Utilisé pour gérer les appareils multimédias.                                                                                                            |
| `Call`                              | Utilisé pour représenter un appel                                                                                                              |
| `LocalVideoStream`                  | Utilisé pour créer un flux vidéo local pour un appareil photo sur le système local.                                                          |
| `RemoteParticipant`                 | Utilisé pour représenter un participant distant dans l’appel                                                                                   |
| `RemoteVideoStream`                 | Utilisé pour représenter un flux vidéo distant d’un participant distant.                                                                  |

> [!NOTE]
> Les instances d’objet du SDK d’appel ne doivent pas être considérées comme des objets JavaScript ordinaires. Il s’agit d’instances réelles de différentes classes qui ne peuvent donc pas être sérialisées.

### <a name="events-model"></a>Modèle d’événements
Chaque objet dans le SDK d’appel a des propriétés et des collections dont les valeurs changent tout au long de la durée de vie de l’objet.
Utilisez la méthode `on()` pour vous abonner aux événements d’objets, et utilisez la méthode `off()` pour vous désabonner des événements d’objets.

#### <a name="properties"></a>Propriétés
- Vous devez examiner leurs valeurs initiales et vous abonner à l’événement `'<property>Changed'` pour les futures mises à jour de valeur.

#### <a name="collections"></a>Collections
- Vous devez examiner leurs valeurs initiales et vous abonner à l’événement `'<collection>Updated'` pour les futures mises à jour de valeur.
- La charge utile de l’événement `'<collection>Updated'` possède un tableau `added` qui contient des valeurs qui ont été ajoutées à la collection.
- La charge utile de l’événement `'<collection>Updated'` possède également un tableau `removed` qui contient des valeurs qui ont été supprimées de la collection.

```js
/*************************************
 * Example code - client.js          *
 * Convert this script into a        *
 * bundle.js that your html index    *
 * page can use.                     *
 *************************************/

// Make sure to install the necessary dependencies
const { CallClient, VideoStreamRenderer, LocalVideoStream } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential } = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the log level and output
setLogLevel('verbose');
AzureLogger.log = (...args) => {
    console.log(...args);
};

// Calling web sdk objects
let callAgent;
let deviceManager;
let call;
let incomingCall;
let localVideoStream;
let localVideoStreamRenderer;

// UI widgets
let userAccessToken = document.getElementById('user-access-token');
let calleeAcsUserId = document.getElementById('callee-acs-user-id');
let initializeCallAgentButton = document.getElementById('initialize-call-agent');
let startCallButton = document.getElementById('start-call-button');
let acceptCallButton = document.getElementById('accept-call-button');
let startVideoButton = document.getElementById('start-video-button');
let stopVideoButton = document.getElementById('stop-video-button');
let connectedLabel = document.getElementById('connectedLabel');
let remoteVideoContainer = document.getElementById('remoteVideoContainer');
let localVideoContainer = document.getElementById('localVideoContainer');

// Instantiate the Call Agent.
initializeCallAgentButton.onclick = async () => {
    try {
        const callClient = new CallClient(); 
        tokenCredential = new AzureCommunicationTokenCredential(userAccessToken.value.trim());
        callAgent = await callClient.createCallAgent(tokenCredential)
        // Set up a camera device to use.
        deviceManager = await callClient.getDeviceManager();
        await deviceManager.askDevicePermission({ video: true });
        await deviceManager.askDevicePermission({ audio: true });
        // Listen for an incoming call to accept.
        callAgent.on('incomingCall', async (args) => {
            try {
                incomingCall = args.incomingCall;
                acceptCallButton.disabled = false;
                startCallButton.disabled = true;
            } catch (error) {
                console.error(error);
            }
        });

        startCallButton.disabled = false;
        initializeCallAgentButton.disabled = true;
    } catch(error) {
        console.error(error);
    }
}

// Start an out-going call.
startCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = callAgent.startCall([{ communicationUserId: calleeAcsUserId.value.trim() }], { videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Accept the incoming call.
acceptCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = await incomingCall.accept({ videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a call obj.
// Listen for property changes and collection udpates.
subscribeToCall = (call) => {
    try {
        // Inspect the initial call.id value.
        console.log(`Call Id: ${call.id}`);
        //Subsribe to call's 'idChanged' event for value changes.
        call.on('idChanged', () => {
            console.log(`Call Id changed: ${call.id}`); 
        });

        // Inspect the initial call.state value.
        console.log(`Call state: ${call.state}`);
        // Subscribe to call's 'stateChanged' event for value changes.
        call.on('stateChanged', async () => {
            console.log(`Call state changed: ${call.state}`);
            if(call.state === 'Connected') {
                connectedLabel.hidden = false;
                acceptCallButton.disabled = true;
                startCallButton.disabled = true;
                startVideoButton.disabled = false;
                stopVideoButton.disabled = false
            } else if (call.state === 'Disconnected') {
                connectedLabel.hidden = true;
                startCallButton.disabled = false;
                console.log(`Call ended, call end reason={code=${call.callEndReason.code}, subCode=${call.callEndReason.subCode}}`);
            }   
        });

        call.localVideoStreams.forEach(async (lvs) => {
            localVideoStream = lvs;
            await displayLocalVideoStream();
        });
        call.on('localVideoStreamsUpdated', e => {
            e.added.forEach(async (lvs) => {
                localVideoStream = lvs;
                await displayLocalVideoStream();
            });
            e.removed.forEach(lvs => {
               removeLocalVideoStream();
            });
        });
        
        // Inspect the call's current remote participants and subscribe to them.
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        // Subscribe to the call's 'remoteParticipantsUpdated' event to be
        // notified when new participants are added to the call or removed from the call.
        call.on('remoteParticipantsUpdated', e => {
            // Subscribe to new remote participants that are added to the call.
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            // Unsubscribe from participants that are removed from the call
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant obj.
// Listen for property changes and collection udpates.
subscribeToRemoteParticipant = (remoteParticipant) => {
    try {
        // Inspect the initial remoteParticipant.state value.
        console.log(`Remote participant state: ${remoteParticipant.state}`);
        // Subscribe to remoteParticipant's 'stateChanged' event for value changes.
        remoteParticipant.on('stateChanged', () => {
            console.log(`Remote participant state changed: ${remoteParticipant.state}`);
        });

        // Inspect the remoteParticipants's current videoStreams and subscribe to them.
        remoteParticipant.videoStreams.forEach(remoteVideoStream => {
            subscribeToRemoteVideoStream(remoteVideoStream)
        });
        // Subscribe to the remoteParticipant's 'videoStreamsUpdated' event to be
        // notified when the remoteParticiapant adds new videoStreams and removes video streams.
        remoteParticipant.on('videoStreamsUpdated', e => {
            // Subscribe to new remote participant's video streams that were added.
            e.added.forEach(remoteVideoStream => {
                subscribeToRemoteVideoStream(remoteVideoStream)
            });
            // Unsubscribe from remote participant's video streams that were removed.
            e.removed.forEach(remoteVideoStream => {
                console.log('Remote participant video stream was removed.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant's remote video stream obj.
// Listen for property changes and collection udpates.
// When their remote video streams become available, display them in the UI.
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.hidden = false;
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}

// Start your local video stream.
// This will send your local video stream to remote participants so they can view it.
startVideoButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        await call.startVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Stop your local video stream.
// This will stop your local video stream from being sent to remote participants.
stopVideoButton.onclick = async () => {
    try {
        await call.stopVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Create a local video stream for your camera device
createLocalVideoStream = async () => {
    const camera = (await deviceManager.getCameras())[0];
    if (camera) {
        return new LocalVideoStream(camera);
    } else {
        console.error(`No camera device found on the system`);
    }
}

// Display your local video stream preview in your UI
displayLocalVideoStream = async () => {
    try {
        localVideoStreamRenderer = new VideoStreamRenderer(localVideoStream);
        const view = await localVideoStreamRenderer.createView();
        localVideoContainer.hidden = false;
        localVideoContainer.appendChild(view.target);
    } catch (error) {
        console.error(error);
    } 
}

// Remove your local video stream preview from your UI
removeLocalVideoStream = async() => {
    try {
        localVideoStreamRenderer.dispose();
        localVideoContainer.hidden = true;
    } catch (error) {
        console.error(error);
    } 
}
```

Exemple de code HTML pouvant utiliser un bundle généré à partir de l’exemple JavaScript ci-dessus (`client.js`).
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
    <head>
        <title>Azure Communication Services - Calling Web SDK</title>
    </head>
    <body>
        <h4>Azure Communication Services - Calling Web SDK</h4>
        <input id="user-access-token"
            type="text"
            placeholder="User access token"
            style="margin-bottom:1em; width: 500px;"/>
        <button id="initialize-call-agent" type="button">Initialize Call Agent</button>
        <br>
        <br>
        <input id="callee-acs-user-id"
            type="text"
            placeholder="Enter callee's ACS user identity in format: '8:acs:resourceId_userId'"
            style="margin-bottom:1em; width: 500px; display: block;"/>
        <button id="start-call-button" type="button" disabled="true">Start Call</button>
        <button id="accept-call-button" type="button" disabled="true">Accept Call</button>
        <button id="start-video-button" type="button" disabled="true">Start Video</button>
        <button id="stop-video-button" type="button" disabled="true">Stop Video</button>
        <br>
        <br>
        <div id="connectedLabel" style="color: #13bb13;" hidden>Call is connected!</div>
        <br>
        <div id="remoteVideoContainer" style="width: 40%;" hidden>Remote participants' video streams:</div>
        <br>
        <div id="localVideoContainer" style="width: 30%;" hidden>Local video stream:</div>
        <!-- points to the bundle generated from client.js -->
        <script src="./bundle.js"></script>
    </body>
</html>
```
Notez qu’après avoir démarré, rejoint ou accepté un appel, vous pouvez également utiliser l’événement « callsUpdated » de callAgent pour recevoir des notifications concernant le nouvel objet d’appel et commencer à s’y abonner.
```js
callAgent.on('callsUpdated', e => {
    // New Call object is added to callAgent.calls collection
    e.added.forEach(call => {
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        call.on('remoteParticipantsUpdated', e => {
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    })
})
```

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Initialiser une instance CallClient, créer une instance CallAgent et accéder à deviceManager

Créez une instance `CallClient`. Vous pouvez le configurer à l’aide d’options personnalisées comme une instance d’enregistreur d’événements.

Quand vous avez une instance `CallClient`, vous pouvez créer une instance `CallAgent` en appelant la méthode `createCallAgent` sur l’instance `CallClient`. Cette méthode renvoie un objet d’instance `CallAgent` de manière asynchrone.

La méthode `createCallAgent` utilise `CommunicationTokenCredential` comme argument. Elle accepte un [jeton d’accès utilisateur](../../../access-tokens.md).

Vous pouvez utiliser la méthode `getDeviceManager` sur l’instance `CallClient` pour accéder à `deviceManager`.

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the logger's log level
setLogLevel('verbose');
// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};
const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Passer un appel

Pour créer et démarrer un appel, utilisez l’une des API sur `callAgent` et fournissez un utilisateur que vous avez créé par le biais du SDK Identité Communication Services.

La création et le démarrage de l’appel sont synchrones. L’instance `call` vous permet de vous abonner à des événements d’appel.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Passer un appel 1:n à un utilisateur ou à un RTC

Pour appeler un autre utilisateur de Communication Services, utilisez la méthode `startCall` sur `callAgent` et transmettez le `CommunicationUserIdentifier` du destinataire que vous avez [créé avec la bibliothèque d’administration de Communication Services](../../../access-tokens.md).

Pour un appel 1:1 à un utilisateur, utilisez le code suivant :

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Pour passer un appel à un réseau téléphonique commuté (RTC) public, utilisez la méthode `startCall` sur `callAgent` et transmettez le `PhoneNumberIdentifier` du destinataire. Votre ressource Communication Services doit être configurée pour autoriser l’appel RTC.

Quand vous appelez un numéro RTC, spécifiez votre ID d’appelant de substitution. Un ID d’appelant de substitution est un numéro de téléphone (basé sur la norme E.164) qui identifie l’appelant dans un appel RTPC. Il s’agit du numéro de téléphone que le destinataire de l’appel voit en tant qu’appel entrant.

> [!NOTE]
> L’appel RTC est actuellement en préversion privée. Pour l’accès, [appliquez le programme Utilisateur précoce](https://aka.ms/ACS-EarlyAdopter).

Pour un appel 1:1 à un numéro de réseau téléphonique commuté, utilisez le code suivant :
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Pour un appel 1:n à un utilisateur et un numéro de réseau téléphonique commuté, utilisez le code suivant :

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Passer un appel 1:1 avec une caméra vidéo

> [!IMPORTANT]
> Il ne peut actuellement y avoir qu’un seul flux vidéo local sortant.

Pour passer un appel vidéo, vous devez énumérer des caméras locales avec la méthode `getCameras()` dans `deviceManager`.

Après avoir sélectionné une caméra, utilisez-la pour construire une instance `LocalVideoStream`. Transmettez-la dans `videoOptions` en tant qu’élément du tableau `localVideoStream` à la méthode `startCall`.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([userCallee], placeCallOptions);
```

- Quand votre appel est connecté, il commence automatiquement à envoyer un flux vidéo à l’autre participant à partir de la caméra sélectionnée. Ceci s’applique également aux options vidéo `Call.Accept()` et aux options vidéo `CallAgent.join()`.


### <a name="join-a-group-call"></a>Rejoindre un appel de groupe

> [!NOTE]
> Considéré comme des métadonnées système, le paramètre `groupId` peut être utilisé par Microsoft pour les opérations nécessaires à l’exécution du système. N’incluez pas de données personnelles dans la valeur `groupId`. Microsoft ne traite pas ce paramètre comme des données personnelles et son contenu peut être visible pour les employés de Microsoft ou stocké à long terme.
>
> Le paramètre `groupId` exige que les données soient au format GUID. Nous vous recommandons d’utiliser des GUID générés de manière aléatoire qui ne sont pas considérés comme des données personnelles dans vos systèmes.
>

Pour démarrer un nouvel appel de groupe ou rejoindre un appel de groupe, utilisez la méthode `join` et transmettez un objet avec une propriété `groupId`. La valeur `groupId` doit être un GUID.

```js

const context = { groupId: '<GUID>'}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Participer à une réunion Teams
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

Pour participer à une réunion Teams, utilisez la méthode `join` et transmettez un lien de réunion ou les coordonnées d’une réunion.

Participer avec un lien de réunion :

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

Participer avec des coordonnées de réunion :

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Recevoir un appel entrant

L’instance de `callAgent` émet un événement `incomingCall` quand l’identité connectée reçoit un appel entrant. Pour écouter cet événement, abonnez-vous en utilisant l’une des options suivantes :

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

L’événement `incomingCall` comprend une instance `incomingCall` que vous pouvez accepter ou rejeter.

Lorsque vous démarrez/rejoignez/acceptez un appel avec la vidéo activée, si la caméra spécifiée est utilisée par un autre processus ou si elle est désactivée dans le système, l’appel commence sans vidéo, et un diagnostic d’appel cameraStartFailed : true est déclenché.

Consultez la section Diagnostics d’appel pour savoir comment gérer ce diagnostic d’appel.

## <a name="manage-calls"></a>Gérer les appels

Pendant un appel, vous pouvez accéder aux propriétés de l’appel et gérer les paramètres vidéo et audio.

### <a name="check-call-properties"></a>Vérifier les propriétés de l’appel

Obtenez l’ID unique (string) d’un appel :

   ```js
    const callId: string = call.id;
   ```
Obtenir des informations sur l’appel :
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS
   ```js
   const callInfo = call.info;
   ```

Découvrez les autres participants à l’appel en examinant la collection `remoteParticipants` sur l’instance « call » :

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identifiez l’appelant d’un appel entrant :

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` correspond à l’un des types `CommunicationIdentifier`.

Obtenez l’état d’un appel :

   ```js
   const callState = call.state;
   ```

   Retourne une chaîne représentant l’état actuel d’un appel :

  - `None` : état initial de l’appel.
  - `Connecting` : état de transition initial quand un appel est passé ou accepté.
  - `Ringing` : pour un appel sortant, indique qu’il sonne pour les participants distants. Il s’agit de `Incoming` de leur côté.
  - `EarlyMedia` : indique un état dans lequel une annonce est lue avant la connexion de l’appel.
  - `Connected` : indique que l’appel est connecté.
  - `LocalHold` : indique que l’appel est mis en attente par un participant local. Aucun média ne circule entre le point de terminaison local et les participants distants.
  - `RemoteHold` : indique que l’appel a été mis en attente par un participant distant. Aucun média ne circule entre le point de terminaison local et les participants distants.
  - `InLobby` : indique que l’utilisateur est en salle d’attente.
  - `Disconnecting` : état de transition avant que l’appel ne passe à l’état `Disconnected`.
  - `Disconnected` : état d’appel final. Si la connexion réseau est perdue, l’état passe à `Disconnected` au bout de deux minutes.

Découvrez pourquoi un appel s’est terminé en examinant la propriété `callEndReason` :

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Découvrez si l’appel en cours est entrant ou sortant en inspectant la propriété `direction`. Elle retourne `CallDirection`.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Vérifiez si le microphone actuel est désactivé. Elle retourne `Boolean`.

   ```js
   const muted = call.isMuted;
   ```

Découvrez si le flux de partage d’écran est envoyé à partir d’un point de terminaison donné en vérifiant la propriété `isScreenSharingOn`. Elle retourne `Boolean`.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Inspectez les flux vidéo actifs en vérifiant la collection `localVideoStreams`. Elle retourne des objets `LocalVideoStream`.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Activer et désactiver le son

Pour activer ou désactiver le son du point de terminaison local, vous pouvez utiliser les API asynchrones `mute` et `unmute` :

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Démarrer et arrêter l’envoi de vidéo locale

Pour démarrer une vidéo, vous devez énumérer les caméras à l’aide de la méthode `getCameras` sur l’objet `deviceManager`. Créez ensuite une nouvelle instance de `LocalVideoStream` avec la caméra souhaitée, puis transmettez l’objet `LocalVideoStream` dans la méthode `startVideo` :

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Après que vous avez commencé à envoyer une vidéo, une instance `LocalVideoStream` est ajoutée à la collection `localVideoStreams` sur une instance d’appel.

```js
call.localVideoStreams[0] === localVideoStream;
```

Pour arrêter la vidéo locale, transmettez l’instance `localVideoStream` qui est disponible dans la collection `localVideoStreams` :

```js
await call.stopVideo(localVideoStream);
// or
await call.stopVideo(call.localVideoStreams[0]);
```
Notez qu’il existe 4 API dans lesquelles vous pouvez transmettre une instance localVideoStream pour démarrer la vidéo dans un appel : callAgent.startCall(), callAgent.join(), call.accept() et call.startVideo(). Pour l’API call.stopVideo(), vous devez transmettre la même instance localVideoStream que celle que vous avez transmise dans ces 4 API.

Vous pouvez basculer vers une autre caméra pendant qu’une vidéo est envoyée en appelant `switchSource` sur une instance `localVideoStream` :

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

Si le périphérique vidéo spécifié est utilisé par un autre processus ou s’il est désactivé dans le système :
- Pendant un appel, si votre vidéo est désactivée et que vous la démarrez à l’aide de l’API call.startVideo(), cette API génère un code SourceUnavailableError, et un diagnostic d’appel cameraStartFiled : true est déclenché.
- Un appel à l’API localVideoStream.switchSource() entraîne la génération d’un diagnostic d’appel cameraStartFailed : true.
Consultez la section Diagnostics d’appel pour savoir comment gérer ces diagnostics d’appel.

## <a name="manage-remote-participants"></a>Gérer les participants distants

Tous les participants distants sont représentés par le type `RemoteParticipant`, et disponibles via la collection `remoteParticipants` sur une instance d’appel.

### <a name="list-the-participants-in-a-call"></a>Lister les participants à un appel

La collection `remoteParticipants` retourne une liste de participants distants à un appel :

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Accéder aux propriétés des participants distants

Un ensemble de propriétés et de collections sont associées aux participants distants :

- `CommunicationIdentifier` : obtenez l’identificateur d’un participant distant. L’identité correspond à l’un des types `CommunicationIdentifier` :

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Les types `CommunicationIdentifier` possibles sont les suivants :

  - `{ communicationUserId: '<ACS_USER_ID'> }` : objet représentant l’utilisateur d’ACS.
  - `{ phoneNumber: '<E.164>' }` : objet représentant le numéro de téléphone au format E.164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }` : objet représentant l’utilisateur de Teams.
  - `{ id: string }` : objet représentant un identificateur qui ne correspond à aucun autre type d’identificateur

- `state` : obtenez l’état d’un participant distant.

  ```js
  const state = remoteParticipant.state;
  ```

  L’état peut être :

  - `Idle` : état initial.
  - `Connecting` : état transitoire pendant qu’un participant se connecte à l’appel.
  - `Ringing` : sonnerie en cours chez le participant.
  - `Connected` : le participant est connecté à l’appel.
  - `Hold` : le participant est en attente.
  - `EarlyMedia` : annonce qui est lue avant qu’un participant ne se connecte à l’appel.
  - `InLobby` : indique que le participant distant est en salle d’attente.
  - `Disconnected` : état final. Le participant est déconnecté de l’appel. Si le participant distant perd sa connectivité réseau, son état passe à `Disconnected` au bout de deux minutes.

- `callEndReason` : pour savoir pourquoi un participant a quitté l’appel, vérifiez la propriété `callEndReason` :

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- État `isMuted` : pour savoir si le son d’un participant est désactivé, vérifiez la propriété `isMuted`. Elle retourne `Boolean`.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- État `isSpeaking` : pour savoir si un participant parle, vérifiez la propriété `isSpeaking`. Elle retourne `Boolean`.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams` : pour inspecter tous les flux vidéo qu’un participant donné envoie dans cet appel, vérifiez la collection `videoStreams`. Elle contient des objets `RemoteVideoStream`.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName` : pour obtenir le nom complet de ce participant distant, inspectez la propriété `displayName`. Elle retourne une chaîne. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Ajouter un participant à un appel

Pour ajouter un participant (un utilisateur ou un numéro de téléphone) à un appel, vous pouvez appeler `addParticipant`. Fournissez l’un des types `Identifier`. Il renvoie l’instance `remoteParticipant` de manière synchrone. L’événement `remoteParticipantsUpdated` à partir de l’appel est déclenché lorsqu’un participant est correctement ajouté à l’appel.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Supprimer un participant d’un appel

Pour supprimer un participant (un utilisateur ou un numéro de téléphone) d’un appel, vous pouvez appeler `removeParticipant`. Vous devez passer l’un des types `Identifier`. Cette méthode résout le problème de façon asynchrone une fois le participant supprimé de l’appel. Le participant est également supprimé de la collection `remoteParticipants`.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Afficher les flux vidéo des participants distants

Pour répertorier les flux vidéo et les flux de partage d’écran des participants distants, inspectez les collections `videoStreams` :

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Pour afficher `RemoteVideoStream`, vous devez vous abonner à son événement `isAvailableChanged`. Si la propriété `isAvailable` prend la valeur `true`, un participant distant envoie un flux. Une fois que cela se produit, créez une instance de `VideoStreamRenderer`, puis une instance `VideoStreamRendererView` en utilisant la méthode `createView` asynchrone.  Vous pouvez ensuite attacher `view.target` à un élément d’interface utilisateur quelconque.

Chaque fois que la disponibilité d’un flux distant change, vous pouvez choisir de détruire l’intégralité de `VideoStreamRenderer`, un `VideoStreamRendererView` spécifique, ou de les conserver, mais cela entraîne l’affichage d’une image vidéo vide.

```js
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const remoteVideoContainer = document.getElementById('remoteVideoContainer');
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriétés du flux vidéo distant

Les flux vidéo distants ont les propriétés suivantes :

- `id` : ID d’un flux vidéo distant.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType` : Peut être `Video` ou `ScreenSharing`.

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable` : indique si le point de terminaison du participant distant envoie activement un flux.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Méthodes et propriétés de VideoStreamRenderer
Créez une instance `VideoStreamRendererView` qui peut être jointe dans l’interface utilisateur de l’application pour afficher le flux vidéo distant. Utilisez la méthode `createView()` asynchrone ; elle est résolue quand le flux est prêt à être affiché et retourne un objet avec une propriété `target` qui représente l’élément `video` pouvant être ajouté n’importe où dans l’arborescence DOM.

  ```js
  await videoStreamRenderer.createView()
  ```

Supprimez `videoStreamRenderer` et toutes les instances `VideoStreamRendererView` associées :

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Méthodes et propriétés de VideoStreamRendererView

Quand vous créez un `VideoStreamRendererView`, vous pouvez spécifier les propriétés `scalingMode` et `isMirrored`. `scalingMode` Peut être `Stretch`, `Crop` ou `Fit`. Si `isMirrored` est spécifié, le flux affiché est retourné verticalement.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Chaque instance `VideoStreamRendererView` a une propriété `target` qui représente la surface du rendu. Attachez cette propriété dans l’interface utilisateur de l’application :

```js
htmlElement.appendChild(view.target);
```

Vous pouvez mettre à jour `scalingMode` en appelant la méthode `updateScalingMode` :

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestion des appareils

Dans `deviceManager`, vous pouvez énumérer des appareils locaux qui peuvent transmettre vos flux audio et vidéo dans un appel. Vous pouvez également l’utiliser pour demander l’autorisation d’accéder aux microphones et aux caméras de l’appareil local.

Vous pouvez accéder à `deviceManager` en appelant la méthode `callClient.getDeviceManager()` :

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Récupérer les appareils locaux

Pour accéder aux appareils locaux, vous pouvez utiliser les méthodes d’énumération sur `deviceManager`. L’énumération est une action asynchrone

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Définir le microphone et le haut-parleur par défaut

Dans `deviceManager`, vous pouvez définir un appareil par défaut que vous utiliserez pour démarrer un appel. Si les paramètres par défaut du client ne sont pas définis, Communication Services utilise les paramètres par défaut du système d’exploitation.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Aperçu de la caméra locale

Vous pouvez utiliser `deviceManager` et `VideoStreamRenderer` pour commencer à afficher le flux de votre caméra locale. Ce flux n’est pas envoyé à d’autres participants ; il s’agit d’un flux d’aperçu local.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Demander l’autorisation d’accès à une caméra et à un microphone

Demandez à un utilisateur d’accorder l’autorisation d’accéder à sa caméra et/ou à son microphone :

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Cela est résolu avec un objet qui indique si les autorisations `audio` et `video` ont été accordées :

```js
console.log(result.audio);
console.log(result.video);
```
#### <a name="notes"></a>Notes
- L’événement « videoDevicesUpdated » se déclenche lorsque des appareils vidéo sont en cours de branchement/débranchés.
- L’événement « audioDevicesUpdated » se déclenche lorsque des appareils audio sont branchés.
- À sa création, le DeviceManager ne reconnaît aucun appareil si les autorisations n’ont pas encore été accordées, et les listes d’appareils sont donc vides au début. Si nous appelons ensuite l’API DeviceManager.askPermission(), l’utilisateur est invité à autoriser l’accès à l’appareil et si l’utilisateur clique sur « autoriser » pour accorder l’accès, le gestionnaire de périphériques reconnaît les appareils du système, met à jour ses listes d’appareils et émet les événements « audioDevicesUpdated » et « videoDevicesUpdated ». Imaginons que nous actualisions ensuite la page et créions un gestionnaire de périphériques. Ce dernier pourra reconnaître les appareils, car l’utilisateur y a déjà accordé l’accès. Par conséquent, ses listes d’appareils seront déjà remplies et il n’émettra pas d’événements « audioDevicesUpdated » ni « videoDevicesUpdated ».
- L’énumération/sélection d’intervenants n’est pas prise en charge sur Android Chrome, iOS Safari ni macOS Safari.

## <a name="call-feature-extensions"></a>Extensions de la fonctionnalité d’appel

### <a name="record-calls"></a>Enregistrer les appels
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

L’enregistrement des appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité d’enregistrement :

```js
const callRecordingApi = call.api(Features.Recording);
```

Ensuite, pour vérifier si l’appel est en train d’être enregistré, inspectez la propriété `isRecordingActive` de `callRecordingApi`. Elle retourne `Boolean`.

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Vous pouvez également vous abonner à l’enregistrement des modifications :

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

### <a name="transfer-calls"></a>Transférer les appels
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

Le transfert d’appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité de transfert :

```js
const callTransferApi = call.api(Features.Transfer);
```

Les transferts d’appels impliquent trois parties :

- *Transférant*  : personne qui lance la demande de transfert.
- *Transféré* : personne qui est transférée.
- *Cible de transfert* : personne vers laquelle le transfert est effectué.

Les transferts suivent les étapes ci-dessous :

1. Il existe déjà un appel connecté entre le *transférant* et le *transféré*. Le *transférant* décide de transférer l’appel depuis le *transféré* vers la *cible de transfert*.
1. Le *transférant* appelle l’API `transfer`.
1. Le *transféré* décide de faire un `accept` ou un `reject` de la demande de transfert vers la *cible de transfert* en utilisant un événement `transferRequested`.
1. La *cible de transfert* reçoit un appel entrant seulement si le *transféré* accepte la demande de transfert.

Pour transférer un appel en cours, vous pouvez utiliser l’API `transfer`. `transfer` prend les `transferCallOptions` facultatives, ce qui vous permet de définir un indicateur `disableForwardingAndUnanswered` :

- `disableForwardingAndUnanswered = false` : si la *cible de transfert* ne répond pas à l’appel transféré, le transfert suit les paramètres de transfert et de non-réponse de la *cible de transfert*.
- `disableForwardingAndUnanswered = true` : si la *cible de transfert* ne répond pas à l’appel transféré, la tentative de transfert se termine.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

L’API `transfer` vous permet de vous abonner aux événements `transferStateChanged` et `transferRequested`. Un événement `transferRequested` provient d’une instance `call` ; un événement `transferStateChanged` et les informations `state` et `error` du transfert proviennent d’une instance `transfer`.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Le *transféré* peut accepter ou rejeter la demande de transfert lancée par le *transférant* dans l’événement `transferRequested` en utilisant `accept()` ou `reject()` dans `transferRequestedEventArgs`. Vous pouvez accéder aux informations de `targetParticipant` et aux méthodes `accept` ou `reject` dans `transferRequestedEventArgs`.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```

### <a name="dominant-speakers-of-a-call"></a>Intervenants dominants d’un appel
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

Les intervenants dominants d’un appel sont une fonctionnalité étendue de l’API `Call` de base et vous permettent d’obtenir la liste des intervenants actifs pendant l’appel. 

Il s’agit d’une liste classée, dont le premier élément représente le dernier intervenant actif de l’appel, et ainsi de suite.

Pour obtenir les intervenants dominants d’un appel, vous devez d’abord obtenir l’objet de l’API de fonctionnalité des intervenants dominants d’appel :

```js
const callDominantSpeakersApi = call.api(Features.CallDominantSpeakers);
```

Ensuite, vous devez obtenir la liste des intervenants dominants en appelant `dominantSpeakers`. Elle est du type `DominantSpeakersInfo`, qui contient les membres suivants :

- `speakersList` contient la liste des intervenants dominants de l’appel classés. Ces derniers sont représentés par leur ID de participant.
- `timestamp` correspond à l’heure de la dernière mise à jour des intervenants dominants de l’appel.

```js
let dominantSpeakers: DominantSpeakersInfo = callDominantSpeakersApi.dominantSpeakers;
```
Vous pouvez également vous abonner à l’événement `dominantSpeakersChanged` pour savoir quand la liste d’intervenants dominants a changé

```js
const dominantSpeakersChangedHandler = () => {
    // Get the most up to date list of dominant speakers
    let dominantSpeakers = callDominantSpeakersApi.dominantSpeakers;
};
callDominantSpeakersApi.api(Features.CallDominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);
``` 
#### <a name="handle-the-dominant-speakers-video-streams"></a>Gérer les flux vidéo d’intervenant dominant

Votre application peut utiliser la fonctionnalité `DominantSpeakers` pour restituer un ou plusieurs flux vidéo d’intervenants dominants et mettre à jour l’interface utilisateur à chaque modification de la liste d’intervenants dominants. Vous pouvez y parvenir avec l’exemple de code suivant.

```js
// RemoteParticipant obj representation of the dominant speaker
let dominantRemoteParticipant: RemoteParticipant;
// It is recommended to use a map to keep track of a stream's associated renderer
let streamRenderersMap: new Map<RemoteVideoStream, VideoStreamRenderer>();

function getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier) {
    let dominantRemoteParticipant: RemoteParticipant;
    switch(dominantSpeakerIdentifier.kind) {
        case 'communicationUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as CommunicationUserIdentifier).communicationUserId === dominantSpeakerIdentifier.communicationUserId
            });
            break;
        }
        case 'microsoftTeamsUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as MicrosoftTeamsUserIdentifier).microsoftTeamsUserId === dominantSpeakerIdentifier.microsoftTeamsUserId
            });
            break;
        }
        case 'unknown': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as UnknownIdentifier).id === dominantSpeakerIdentifier.id
            });
            break;
        }
    }
    return dominantRemoteParticipant;
}
// Handler function for when the dominant speaker changes
const dominantSpeakersChangedHandler = async () => {
    // Get the new dominant speaker's identifier
    const newDominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];

     if (newDominantSpeakerIdentifier) {
        // Get the remote participant object that matches newDominantSpeakerIdentifier
        const newDominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(newDominantSpeakerIdentifier);

        // Create the new dominant speaker's stream renderers
        const streamViews = [];
        for (const stream of newDominantRemoteParticipant.videoStreams) {
            if (stream.isAvailable && !streamRenderersMap.get(stream)) {
                const renderer = new VideoStreamRenderer(stream);
                streamRenderersMap.set(stream, renderer);
                const view = await videoStreamRenderer.createView();
                streamViews.push(view);
            }
        }

        // Remove the old dominant speaker's video streams by disposing of their associated renderers
        if (dominantRemoteParticipant) {
            for (const stream of dominantRemoteParticipant.videoStreams) {
                const renderer = streamRenderersMap.get(stream);
                if (renderer) {
                    streamRenderersMap.delete(stream);
                    renderer.dispose();
                }
            }
        }

        // Set the new dominant remote participant obj
        dominantRemoteParticipant = newDominantRemoteParticipant

        // Render the new dominant remote participant's streams
        for (const view of streamViewsToRender) {
            htmlElement.appendChild(view.target);
        }
     }
};

// When call is disconnected, set the dominant speaker to undefined
currentCall.on('stateChanged', () => {
    if (currentCall === 'Disconnected') {
        dominantRemoteParticipant = undefined;
    }
});

const dominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];
dominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier);
currentCall.api(Features.DominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);

subscribeToRemoteVideoStream = async (stream: RemoteVideoStream, participant: RemoteParticipant) {
    let renderer: VideoStreamRenderer;

    const displayVideo = async () => {
        renderer = new VideoStreamRenderer(stream);
        streamRenderersMap.set(stream, renderer);
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }

    stream.on('isAvailableChanged', async () => {
        if (dominantRemoteParticipant !== participant) {
            return;
        }

        renderer = streamRenderersMap.get(stream);
        if (stream.isAvailable && !renderer) {
            await displayVideo();
        } else {
            streamRenderersMap.delete(stream);
            renderer.dispose();
        }
    });

    if (dominantRemoteParticipant !== participant) {
        return;
    }

    renderer = streamRenderersMap.get(stream);
    if (stream.isAvailable && !renderer) {
        await displayVideo();
    }
}
```
### <a name="call-diagnostics"></a>Diagnostics d’appel
Les diagnostics d’appel constituent une fonctionnalité étendue de l’API `Call` de base et vous permettent de diagnostiquer un appel actif.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

Les diagnostics présentés aux utilisateurs suivants sont disponibles :

| Type    | Nom                           | Description                                                     | Valeurs possibles                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Cas d'utilisation                                                                       |
| ------- | ------------------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Réseau | noNetwork                      | Aucun réseau disponible.                                  | -Défini sur `True` lorsqu’un appel ne peut pas démarrer, car aucun réseau n’est disponible. <br/> - Défini sur `False` en cas de présence de candidats ICE.                                                                                                                                                                                                                                                                                                                                                      | L’appareil n’est pas connecté à un réseau.                                           |
| Réseau | networkRelaysNotReachable      | Problèmes avec un réseau.                                        | - Défini sur `True` lorsque le réseau comporte une contrainte qui ne vous permet pas d’atteindre les relais ACS. <br/> - Défini sur `False` lors de la réalisation d’un nouvel appel.                                                                                                                                                                                                                                                                                                                                                    | Pendant un appel lorsque le signal WiFi s’active et se désactive.                             |  |
| Réseau | networkReconnect               | La connexion a été perdue et nous nous reconnectons au réseau. | - Défini sur `Poor` lorsque la connectivité de transport médiatique est interrompue                                                                                                                                 <br/> - Défini sur `Bad` lorsque le réseau est déconnecté <br/> - Défini sur `Good` lorsqu’une nouvelle session est connectée.                                                                                                                                                                                           | Faible bande passante, pas d’Internet                                                      |
| Réseau | networkReceiveQuality          | Indicateur concernant la qualité du flux entrant.                 | - Défini sur `Bad` en cas de problème grave de réception du flux. qualité                                                                                                                           <br/> - Défini sur `Poor` en cas de problème modéré de réception du flux. qualité                                                                                                                           <br/> - Défini sur `Good` lorsqu’il n’y a pas de problème de réception du flux. | Bande passante faible                                                                   |
| Média   | noSpeakerDevicesEnumerated     | Il n’y a aucun appareil de sortie audio (haut-parleur) sur le système de l’utilisateur. | - Défini sur `True` lorsque le système ne comporte pas d’appareil haut-parleur, et que la sélection de haut-parleur est prise en charge. <br/> - Défini sur `False` lorsque le système comporte au moins 1 appareil haut-parleur, et que la sélection de haut-parleur n’est pas prise en charge.                                                                                                                                                                                                                                                                                | Tous les haut-parleurs sont débranchés                                                      |
| Média   | speakingWhileMicrophoneIsMuted | Parler avec le micro désactivé.                                   | - Défini sur `True` lorsque le micro local est désactivé et que l’utilisateur local est en train de parler. <br/> - Défini sur `False` lorsque l’utilisateur local arrête de parler ou désactive le micro. <br/> * Remarque : à ce jour, cette fonctionnalité n’est pas encore prise en charge sur Safari, car les échantillons de niveau audio sont extraits de webrtc. stats.                                                                                                                                                                                                     | Pendant un appel, désactivez votre micro et parlez dedans.                          |
| Média   | noMicrophoneDevicesEnumerated  | Le système de l’utilisateur ne comporte aucun appareil de capture audio (micro)      | - Défini sur `True` lorsque le système ne comporte pas de micro. <br/> - Défini sur `False` lorsque le système comporte au moins 1 micro.                                                                                                                                                                                                                                                                                                                                                 | Tous les micros sont débranchés pendant l’appel.                                  |
| Média   | cameraFreeze                   | La caméra cesse de produire des images pendant plus de 5 secondes.          | - Défini sur `True` lorsque le flux de vidéo local est figé. Cela signifie que le côté distant voit votre vidéo figée sur son écran ou que les participants distants ne reçoivent pas votre vidéo sur leur écran. <br/> - Défini sur `False` lorsque le blocage se termine et que les utilisateurs peuvent voir votre vidéo normalement.                                                                                                                                                                                         | La caméra a été perdue pendant l’appel ou un mauvais réseau a entraîné le blocage de la caméra. |
| Média   | cameraStartFailed              | Échec générique de la caméra.                                         | - Défini sur `True` en cas d’échec de l’envoi de vidéo locale parce que la caméra a peut-être été désactivée dans le système ou qu’elle est utilisée par un autre processus. <br/> - Défini sur `False` lorsque la caméra sélectionnée parvient à envoyer la vidéo locale. .                                                                                                                                                                                                                                           | Défaillances de caméra                                                                 |
| Média   | cameraStartTimedOut            | Scénario courant où la caméra est dans un mauvais état de fonctionnement.                   | - Défini sur `True` en cas d’expiration du délai d’attente d’envoi du flux vidéo de la caméra. <br/> - Défini sur `False` lorsque la caméra sélectionnée parvient à nouveau à envoyer la vidéo locale.                                                                                                                                                                                                                                                                                                                                 | Défaillances de caméra                                                                 |
| Média   | microphoneNotFunctioning       | Le micro ne fonctionne pas.                                  | - Défini sur `True` en cas d’échec de l’envoi du flux audio local parce que le micro a peut-être été désactivé dans le système ou qu’il est utilisé par un autre processus. Il faut environ 10 secondes pour déclencher ce périphérique. <br/> - Défini sur `False` lorsque le micro recommence à envoyer correctement le flux audio.                                                                                                                                                                                    | Aucun micro disponible, accès au micro désactivé dans un système                |
| Média   | microphoneMuteUnexpectedly     | Le micro est désactivé                                             | - Défini sur `True` lorsque le micro est désactivé de manière inattendue. <br/> - Défini sur `False` lorsque le micro commence à envoyer correctement le flux audio.                                                                                                                                                                                                                                                                                                                                                     | Le micro est désactivé dans le système.                                            |
| Média   | screenshareRecordingDisabled   | Le partage d’écran du système a été refusé en raison des préférences définies dans Paramètres.     | - Défini sur `True` lorsque l’autorisation de partage d’écran est refusée par les paramètres système (partage). <br/> - Défini sur `False` en cas de réussite d’acquisition de flux. <br/> Remarque : ce diagnostic fonctionne uniquement sous macOS.Chrome.                                                                                                                                                                                                                                                                                               | L’enregistrement d’écran est désactivé dans les Paramètres.                                       |
| Média   | microphonePermissionDenied     | Le volume de l’appareil est faible ou presque silencieux sous macOS. | - Défini sur `True` lorsque l’autorisation audio est refusée par les paramètres système (audio). <br/> - Défini sur `False` en cas de réussite d’acquisition de flux. <br/> Remarque : ce diagnostic fonctionne uniquement sous macOS.                                                                                                                                                                                                                                                                                                                | Les autorisations de micro sont désactivées dans les Paramètres.                            |
| Média   | cameraPermissionDenied         | Les autorisations de caméra ont été refusées dans les paramètres.                     | - Défini sur `True` lorsque l’autorisation de caméra est refusée par les paramètres système (vidéo). <br/> - Défini sur `False` en cas de réussite d’acquisition de flux. <br> Remarque : ce diagnostic fonctionne uniquement sous macOS Chrome                                                                                                                                                                                                                                                                                                          | Les autorisations de caméra sont désactivées dans les paramètres.                                |

- Abonnez-vous à l’événement `diagnosticChanged` pour surveiller tout changement de diagnostic d’un appel.
```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` + 
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

- Obtenez les dernières valeurs de diagnostic d’appel qui ont été générées. Un diagnostic n’est pas défini s’il n’a jamais été déclenché.
```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();
    
console.log(`noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`);
            
console.log(`networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`);
            
console.log(`networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`);


const latestMediaDiagnostics = callDiagnostics.media.getLatest();
    
console.log(`speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`);
            
console.log(`cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`);
            
console.log(`microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`);
```
## <a name="releasing-resources"></a>Libération de ressources
1. Comment libérer correctement les ressources lorsqu’un appel est terminé :
    - Une fois l’appel terminé, notre kit SDK met fin à la signalisation et aux sessions de médias, pour vous laisser une instance de l’appel qui en conserve le dernier état. Vous pouvez toujours vérifier la raison de la fin de l’appel : callEndReason. Si votre application ne conserve pas la référence à l’instance d’appel, le récupérateur de mémoire JavaScript nettoie tout, de sorte à faire revenir votre application à l’état initial avant l’appel concernant la consommation de mémoire.
2. Types de ressources longue durée (durée de vie de l’application) par rapport à celles de courte durée (durée de vie de l’appel) :
    - Les ressources suivantes sont considérées comme étant de « longue durée » : vous pouvez les créer et les référencer pendant une longue période. Elles sont très légères en matière de consommation de ressources (mémoire) et n’affectent donc pas les performances :
        - CallClient
        - CallAgent
        - DeviceManager
    - Les ressources suivantes sont considérées comme étant de « courte durée » et sont celles qui jouent un rôle dans l’appel en soi, émettent des événements dans l’application ou interagissent avec des appareils multimédias locaux. Elles consomment plus de ressources mémoire et de processeur, mais une fois l’appel terminé, le SDK nettoie tout l’état et libère les ressources :
        - Call : car il s’agit de celle qui détient l’état réel de l’appel (signalisation et média).
        - RemoteParticipants : représente les participants distants dans l’appel.
        - VideoStreamRenderer avec VideoStreamRendererViews : gestion de l’affichage vidéo.