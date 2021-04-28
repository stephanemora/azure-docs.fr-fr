---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589905"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un jeton d’accès utilisateur pour activer le client appelant. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../access-tokens.md).
- Facultatif : suivez le guide de démarrage rapide permettant d’[ajouter l’appel vocal à votre application](../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Installer le SDK

> [!NOTE]
> Ce document utilise le SDK web Appel ACS.

Utilisez la commande `npm install` pour installer les kits SDK Appel Azure Communication Services et communs pour JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Point d’entrée principal du SDK Appel.                                                                       |
| `CallAgent`                        | Sert à démarrer et à gérer les appels.                                                                                            |
| `DeviceManager`                    | Utilisé pour gérer les appareils multimédias.                                                                                           |
| `AzureCommunicationTokenCredential` | Implémente l’interface `CommunicationTokenCredential`, qui est utilisée pour instancier `callAgent`. |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Initialiser une instance CallClient, créer une instance CallAgent et accéder à deviceManager

Créez une instance `CallClient`. Vous pouvez le configurer à l’aide d’options personnalisées comme une instance d’enregistreur d’événements.

Quand vous avez une instance `CallClient`, vous pouvez créer une instance `CallAgent` en appelant la méthode `createCallAgent` sur l’instance `CallClient`. Cette opération renvoie un objet d’instance `CallAgent` de manière asynchrone.

La méthode `createCallAgent` utilise `CommunicationTokenCredential` comme argument. Elle accepte un [jeton d’accès utilisateur](../../access-tokens.md).

Vous pouvez utiliser la méthode `getDeviceManager` sur l’instance `CallClient` pour accéder à `deviceManager`.

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Passer un appel

Pour créer et démarrer un appel, utilisez l’une des API sur `callAgent` et fournissez un utilisateur que vous avez créé par le biais du SDK Identité Communication Services.

La création et le démarrage de l’appel sont synchrones. L’instance d’appel vous permet de vous abonner à des événements d’appel.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Passer un appel 1:n à un utilisateur ou à un RTC

Pour appeler un autre utilisateur de Communication Services, utilisez la méthode `startCall` sur `callAgent` et transmettez le `CommunicationUserIdentifier` du destinataire que vous avez [créé avec la bibliothèque d’administration de Communication Services](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Pour passer un appel à un réseau téléphonique commuté (RTC) public, utilisez la méthode `startCall` sur `callAgent` et transmettez le `PhoneNumberIdentifier` du destinataire. Votre ressource Communication Services doit être configurée pour autoriser l’appel RTC.

Quand vous appelez un numéro RTC, spécifiez votre ID d’appelant de substitution. Un ID d’appelant de substitution est un numéro de téléphone (basé sur la norme E.164) qui identifie l’appelant dans un appel RTPC. Il s’agit du numéro de téléphone que le destinataire de l’appel voit en tant qu’appel entrant.

> [!NOTE]
> L’appel RTC est actuellement en préversion privée. Pour l’accès, [appliquez le programme Utilisateur précoce](https://aka.ms/ACS-EarlyAdopter).

Pour un appel 1:1, utilisez le code suivant :

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Pour un appel 1:n, utilisez le code suivant :

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
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
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Quand votre appel est connecté, il commence automatiquement à envoyer un flux vidéo à l’autre participant à partir de la caméra sélectionnée. Ceci s’applique également aux options vidéo `Call.Accept()` et aux options vidéo `CallAgent.join()`.

### <a name="join-a-group-call"></a>Rejoindre un appel de groupe

> [!NOTE]
> Considéré comme des métadonnées système, le paramètre `groupId` peut être utilisé par Microsoft pour les opérations nécessaires à l’exécution du système. N’incluez pas de données personnelles dans la valeur `groupId`. Microsoft ne traite pas ce paramètre comme des données personnelles et son contenu peut être visible pour les employés de Microsoft ou stocké à long terme.
>
> Le paramètre `groupId` exige que les données soient au format GUID. Nous vous recommandons d’utiliser des GUID générés de manière aléatoire qui ne sont pas considérés comme des données personnelles dans vos systèmes.
>

Pour démarrer un nouvel appel de groupe ou rejoindre un appel de groupe, utilisez la méthode `join` et transmettez un objet avec une propriété `groupId`. La valeur `groupId` doit être un GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Participer à une réunion Teams
> [!NOTE]
> Cet API est fourni en tant qu’aperçu pour les développeurs et peut être modifié en fonction des commentaires que nous avons reçus. N’utilisez pas cet API dans un environnement de production. Pour utiliser cette API, utilisez la version bêta du SDK web Appel ACS

Pour participer à une réunion Teams, utilisez la méthode `join` et transmettez un lien de réunion ou les coordonnées d’une réunion.

Participer avec un lien de réunion :

```js
const locator = { meetingLink: <meeting link>}
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
  - `Ringing` : pour un appel sortant, indique qu’il sonne pour les participants distants. Il s’agit `Incoming` de leur côté.
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
```

Vous pouvez basculer vers une autre caméra pendant qu’une vidéo est envoyée en appelant `switchSource` sur une instance `localVideoStream` :

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

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
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Supprimer un participant d’un appel

Pour supprimer un participant (un utilisateur ou un numéro de téléphone) d’un appel, vous pouvez appeler `removeParticipant`. Vous devez passer l’un des types `Identifier`. Le problème est résolu de façon asynchrone une fois le participant supprimé de l’appel. Le participant est également supprimé de la collection `remoteParticipants`.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
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
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
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
  videoStreamRenderer.createView()
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

Demandez à un utilisateur d’accorder l’autorisation d’accéder à sa caméra et à son microphone :

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Cela est résolu avec un objet qui indique si les autorisations `audio` et `video` ont été accordées :

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Enregistrer les appels
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

## <a name="transfer-calls"></a>Transférer les appels
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

## <a name="learn-about-eventing-models"></a>Découvrir les modèles d’événement

Inspectez les valeurs actuelles et abonnez-vous afin de mettre à jour les événements pour les valeurs ultérieures.

### <a name="properties"></a>Propriétés

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Collections

```js
// Inspect the current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});

// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
