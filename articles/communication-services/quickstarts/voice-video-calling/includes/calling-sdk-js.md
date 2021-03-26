---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 41a5f05f016a876894949c35a6610b2b49f9eb9d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488096"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../access-tokens.md)
- Facultatif : Suivez le guide de démarrage rapide pour [prendre en main l’ajout des appels à votre application](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configuration

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

> [!NOTE]
> Ce document utilise la version 1.0.0-beta.6 de la bibliothèque de client appelante.

Utilisez la commande `npm install` pour installer les bibliothèques clientes Azure Communication Services d’appel et communes pour JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client Appel Azure Communication Services :

| Nom                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient est le point d’entrée principal de la bibliothèque de client Appel.                                                                       |
| CallAgent                        | CallAgent sert à démarrer et à gérer les appels.                                                                                            |
| DeviceManager                    | DeviceManager est utilisé pour gérer les appareils multimédias                                                                                           |
| AzureCommunicationTokenCredential | La classe AzureCommunicationTokenCredential implémente l’interface CommunicationTokenCredential qui est utilisée pour instancier CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Initialiser CallClient, créer CallAgent et accéder à DeviceManager

Instanciez une nouvelle instance de `CallClient` . Vous pouvez le configurer à l’aide d’options personnalisées comme une instance d’enregistreur d’événements.
Une fois un `CallClient` instancié, vous pouvez créer une instance `CallAgent` en appelant la méthode `createCallAgent` sur l’instance `CallClient`. Cette opération renvoie un objet d’instance `CallAgent` de manière asynchrone.
La méthode `createCallAgent` prend un `CommunicationTokenCredential` en tant qu’argument, qui accepte un [jeton d’accès utilisateur](../../access-tokens.md).
Pour accéder à `DeviceManager`, une instance callAgent doit d’abord être créée. Vous pouvez ensuite utiliser la méthode `getDeviceManager` sur l’instance `CallClient` pour obtenir DeviceManager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Passer un appel sortant

Pour créer et démarrer un appel, vous devez utiliser l’une des API sur CallAgent et fournir un utilisateur que vous avez créé par le biais de la bibliothèque de client Communication Services Identity.

La création et le démarrage de l’appel sont synchrones. L’instance d’appel vous permet de vous abonner à des événements d’appel.

## <a name="place-a-call"></a>Passer un appel

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Passer un appel 1:1 à un utilisateur ou à un RTC
Pour passer un appel à un autre utilisateur de Communication Services, appelez la méthode `startCall` sur `callAgent`, et transmettez le CommunicationUserIdentifier de l’appelé que vous avez [créé avec la bibliothèque d’administration de Communication Services Identity](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Pour passer un appel à un RTC, appelez la méthode `startCall` sur `callAgent` et passez le PhoneNumberIdentifier de l’appelé.
Votre ressource Communication Services doit être configurée pour autoriser l’appel RTC.
Lors de l’appel d’un numéro RTC, vous devez spécifier votre ID d’appelant de substitution. Un ID d’appelant de substitution fait référence à un numéro de téléphone (basé sur la norme E.164) qui identifie l’appelant dans un appel RTPC. Par exemple, quand vous fournissez un ID d’appelant de substitution à l’appel RTC, ce numéro de téléphone est celui présenté à l’appelé lors de l’arrivée de l’appel.

> [!WARNING]
> L’appel RTC est actuellement en préversion privée. Pour l’accès, [appliquez le programme Utilisateur précoce](https://aka.ms/ACS-EarlyAdopter).
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Passer un appel 1:n avec des utilisateurs et un RTC
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Passer un appel 1:1 avec une caméra vidéo
> [!WARNING]
> Il ne peut actuellement y avoir qu’un seul flux vidéo local sortant.
Pour passer un appel vidéo, vous devez énumérer des caméras locales à l’aide de l’API `getCameras()` de DeviceManager.
Une fois la caméra souhaitée sélectionnée, utilisez-la pour construire une instance `LocalVideoStream` et passer celle-ci dans `videoOptions` en tant qu’élément du tableau `localVideoStream` à la méthode `startCall`.
Une fois votre appel connecté, il commence automatiquement à envoyer un flux vidéo aux autres participants à partir de la caméra sélectionnée. Ceci s’applique également aux options vidéo Call.Accept() et aux options vidéo CallAgent.join().
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Rejoindre un appel de groupe
Pour démarrer un nouvel appel de groupe ou rejoindre un appel de groupe, utilisez la méthode « join » et transmettez un objet avec une propriété `groupId`. La valeur doit être un GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Participer à une réunion Teams
Pour participer à une réunion Teams, utilisez la méthode « join » et transmettez un lien de réunion ou les coordonnées d’une réunion
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Réception d’un appel entrant

L’instance de `CallAgent` émet un événement `incomingCall` quand l’identité connectée reçoit un appel entrant. Pour être à l’écoute de cet événement, abonnez-vous de la façon suivante :

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

L’événement `incomingCall` est fourni avec une instance de `IncomingCall` sur laquelle vous pouvez accepter ou rejeter un appel.


## <a name="call-management"></a>Gestion des appels

Vous pouvez accéder à toutes les propriétés et effectuer différentes opérations lors d’un appel pour gérer les paramètres liés à la vidéo et à l’audio.

### <a name="call-properties"></a>Propriétés d’appel
* Obtenez l’ID unique (string) de cet appel.
```js

const callId: string = call.id;

```

* Pour en savoir plus sur les autres participants à l’appel, examinez la collection `remoteParticipant` sur l’instance `call`. Le tableau contient la liste des objets `RemoteParticipant`.
```js
const remoteParticipants = call.remoteParticipants;
```

* L’identificateur de l’appelant si l’appel est entrant. L’identificateur correspond à l’un des types `CommunicationIdentifier`
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
```js

const callState = call.state;

```
Retourne une chaîne représentant l’état actuel d’un appel :
* « Aucun » – état initial de l’appel
* « Entrant » – indique qu’un appel est entrant ; il doit être accepté ou rejeté
* « Connexion en cours » – état de transition initial une fois l’appel passé ou accepté
* « Sonnerie » – pour un appel sortant, indique que l’appel sonne pour les participants distants, qu’il est « entrant » de leur côté
* « EarlyMedia » – indique un état dans lequel une annonce est lue avant la connexion de l’appel
* « Connecté » – l’appel est connecté
* « En attente locale » – l’appel est mis en attente par un participant local, aucun média ne circule entre le point de terminaison local et les participants distants
* « En attente distante » – l’appel est mis en attente par un participant distant, aucun média ne circule entre le point de terminaison local et les participants distants
* « Déconnexion » – état de transition avant que l’appel passe à l’état « Déconnecté »
* « Déconnecté » – état d’appel final
  * Si la connexion réseau est perdue, l’état passe à « Déconnecté » au bout de 2 minutes environ.

* Pour connaître la raison pour laquelle un appel donné s’est terminé, inspectez la propriété `callEndReason`.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Pour savoir si l’appel en cours est un appel entrant ou sortant, inspectez la propriété `direction`, qui retourne `CallDirection`.
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Pour vérifier si le microphone actuel est désactivé, inspectez la propriété `muted`. Elle retourne un `Boolean`.
```js

const muted = call.isMicrophoneMuted;

```

* Pour voir si le flux de partage d’écran est envoyé à partir d’un point de terminaison donné, vérifiez la propriété `isScreenSharingOn`. Elle retourne un `Boolean`.
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Pour inspecter les flux vidéo actifs, vérifiez la collection `localVideoStreams`. Elle contient des objets `LocalVideoStream`.
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Événement d’appel terminé

L’instance de `Call` émet un événement `callEnded` quand l’appel se termine. Pour être à l’écoute de cet événement, abonnez-vous de la façon suivante :

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
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


Pour démarrer une vidéo, vous devez énumérer les caméras à l’aide de la méthode `getCameras` sur l’objet `deviceManager`. Créez ensuite une instance de `LocalVideoStream` transmettant la caméra souhaitée dans la méthode `startVideo` en tant qu’argument :


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Une fois que vous avez commencé à envoyer une vidéo, une instance `LocalVideoStream`est ajoutée à la collection `localVideoStreams` sur une instance d’appel.

```js

call.localVideoStreams[0] === localVideoStream;

```

Pour arrêter la vidéo locale, transmettez l’instance `localVideoStream` disponible dans la collection `localVideoStreams` :

```js

await call.stopVideo(localVideoStream);

```

Vous pouvez basculer vers une autre caméra pendant l’envoi d’une vidéo en appelant `switchSource` sur une instance `localVideoStream` :

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>Gestion des participants distants

Tous les participants distants sont représentés par le type `RemoteParticipant`, et disponibles via la collection `remoteParticipants` sur une instance d’appel.

### <a name="list-participants-in-a-call"></a>Répertorier les participants à un appel
La collection `remoteParticipants` renvoie une liste de participants distants dans un appel donné :

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Propriétés du participant distant
Un participant distant dispose d’un ensemble de propriétés et de collections qui lui sont associés
#### <a name="communicationidentifier"></a>CommunicationIdentifier
Obtenez l’identificateur de ce participant distant.
L’identité est l’un des types « CommunicationIdentifier » :
```js
const identifier = remoteParticipant.identifier;
```
Il peut s’agir d’un des types de « CommunicationIdentifier » :
  * { communicationUserId: ’<ACS_USER_ID>’ } - objet représentant l’utilisateur ACS
  * { phoneNumber: ’<E.164>’ } - objet représentant le numéro de téléphone au format E.164
  * { microsoftTeamsUserId: ’<TEAMS_USER_ID>’, isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" } - objet représentant l’utilisateur Teams

#### <a name="state"></a>State
Obtenez l’état de ce participant distant.
```js

const state = remoteParticipant.state;
```
Il peut s’agir de l’un des états suivants :
* « Inactif » – état initial
* « Connexion » – état transitoire pendant que le participant se connecte à l’appel
* « Sonnerie » – sonnerie en cours chez le participant
* « Connecté » – le participant est connecté à l’appel
* « En attente » – le participant est en attente
* « EarlyMedia » – une annonce est lue avant que le participant soit connecté à l’appel
* « Déconnecté » – état final ; le participant est déconnecté de l’appel
  * Si un participant à distance perd sa connectivité réseau, son état passe à « Déconnecté » après environ 2 minutes.

#### <a name="call-end-reason"></a>Motif de fin de l’appel
Pour savoir pourquoi le participant a quitté l’appel, inspectez lz propriété `callEndReason` :
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>A le micro coupé
Pour vérifier si ce participant distant est muet ou non, inspectez la propriété `isMuted`. Elle retourne un `Boolean`.
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Est en train de parler
Pour vérifier si ce participant distant parle ou non, inspectez la propriété `isSpeaking`. Elle retourne un `Boolean`.
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Flux vidéo
Pour inspecter tous les flux vidéo qu’un participant donné envoie dans cet appel, vérifiez la collection `videoStreams`. Elle contient des objets `RemoteVideoStream`.
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Ajouter un participant à un appel

Pour ajouter un participant à un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler `addParticipant`.
Fournir l’un des types d’identificateurs.
Cette opération retourne de façon synchrone l’instance de participant distant.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Supprimer un participant d’un appel

Pour supprimer un participant d’un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler `removeParticipant`.
Vous devez passer l’un des types « Identificateur », ce qui sera résolu de façon asynchrone une fois le participant supprimé de l’appel.
Le participant sera également supprimé de la collection `remoteParticipants`.

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

Pour afficher un `RemoteVideoStream`, vous devez vous abonner à un événement `isAvailableChanged`.
Si la propriété `isAvailable` prend la valeur `true`, un participant distant envoie un flux.
Quand cela se produit, créez une instance de `Renderer`, puis une nouvelle instance `RendererView` à l’aide de la méthode `createView` asynchrone.  Vous pouvez ensuite attacher `view.target` à un élément d’interface utilisateur quelconque.
Chaque fois que la disponibilité d’un flux distant change, vous pouvez choisir de détruire le convertisseur tout entier ou un `RendererView` spécifique, ou de les conserver, mais cela entraîne l’affichage d’une image vidéo vide.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriétés du flux vidéo distant
Les flux vidéo distants ont les propriétés suivantes :

* `Id` - ID d’un flux vidéo distant
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` – taille (largeur/hauteur) d’un flux vidéo distant
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` – peut être « video » ou « ScreenSharing »
```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```
* `isAvailable` – indique si le point de terminaison du participant distant envoie activement un flux
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Méthodes et propriétés de convertisseur

* Créez une instance `RendererView` qui peut être jointe ultérieurement dans l’interface utilisateur de l’application pour afficher le flux vidéo distant.
```js
renderer.createView()
```

* Supprimez le convertisseur et toutes les instances `RendererView` associées.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Méthodes et propriétés de RendererView
Lorsque vous créez un `RendererView`, vous pouvez spécifier les propriétés `scalingMode` et `isMirrored`.
Le mode de mise à l’échelle peut être « Stretch », « Crop » ou « Fit ». Si `isMirrored` est spécifié, le flux rendu est retourné verticalement.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
Toute instance `RendererView` donnée a une propriété `target` qui représente la surface du rendu. Cette propriété doit être jointe à l’interface utilisateur de l’application :
```js
document.body.appendChild(rendererView.target);
```

Vous pouvez mettre à jour ultérieurement le mode de mise à l’échelle en appelant la méthode `updateScalingMode`.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Gestion des appareils

`DeviceManager` vous permet d’énumérer les appareils locaux utilisables dans un appel pour transmettre vos flux audio/vidéo. Il vous permet également de demander à un utilisateur l’autorisation d’accéder à son microphone et à sa caméra à l’aide de l’API de navigateur natif.

Vous pouvez accéder au `deviceManager` en appelant la méthode `callClient.getDeviceManager()`.
> [!WARNING]
> Actuellement, un objet `callAgent` doit être préalablement instancié pour avoir accès à DeviceManager

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Énumérer les appareils locaux

Pour accéder aux appareils locaux, vous pouvez utiliser les méthodes d’énumération sur le gestionnaire d’appareils. L’énumération est une action asynchrone.

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Définir le microphone/haut-parleur par défaut

Le gestionnaire d’appareils vous permet de définir un appareil par défaut qui sera utilisé lors du démarrage d’un appel.
Si les valeurs par défaut du client ne sont pas définies, Azure Communication Services revient aux valeurs par défaut du système d’exploitation.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Aperçu de la caméra locale

Vous pouvez utiliser `DeviceManager` et `Renderer` pour commencer à afficher le flux de votre caméra locale. Ce flux n’est pas envoyé à d’autres participants ; il s’agit d’un flux d’aperçu local. Cette action est asynchrone.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Demander l’autorisation d’accès à une caméra ou un microphone

Demandez à un utilisateur d’accorder l’autorisation d’accéder à sa caméra ou à son microphone comme suit :

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
Cela sera résolu de façon asynchrone avec un objet indiquant si les autorisations `audio` et `video` ont été accordées :
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>Gestion de l’enregistrement des appels

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

L’enregistrement des appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité d’enregistrement :

```js
const callRecordingApi = call.api(Features.Recording);
```

Ensuite, pour vérifier si l’appel est en train d’être enregistré, inspectez la propriété `isRecordingActive` de `callRecordingApi`, qui retourne un `Boolean`.

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

## <a name="call-transfer-management"></a>Gestion des transferts d’appels

Le transfert d’appels est une fonctionnalité étendue de l’API `Call` principale. Vous devez d’abord obtenir l’objet d’API de la fonctionnalité de transfert :

```js
const callTransferApi = call.api(Features.Transfer);
```

Le transfert d’appels implique trois parties : le *transférant*, le *transféré* et la *cible de transfert*. Le flux de transfert fonctionne comme suit :

1. Il existe déjà un appel connecté entre le *transférant* et le *transféré*
2. Le *transférant* décider de transférer l’appel (*transféré* -> *cible de transfert*)
3. Le *transférant* appelle l’API `transfer`
4. Le *transféré* décide de faire un `accept` ou un `reject` de la demande de transfert vers la *cible de transfert* via l’événement `transferRequested`.
5. La *cible de transfert* reçoit un appel entrant seulement si le *transféré* a fait un `accept` de la demande de transfert

### <a name="transfer-terminology"></a>Terminologie des transferts

- Transférant : celui qui lance la demande de transfert.
- Transféré : celui qui est transféré par le transférant vers la cible de transfert.
- Cible de transfert : celui qui est la cible vers laquelle le transfert est effectué

Pour transférer un appel en cours, vous pouvez utiliser l’API synchrone `transfer`. `transfer` prend des `TransferCallOptions` facultatives, ce qui vous permet de définir l’indicateur `disableForwardingAndUnanswered` :

- `disableForwardingAndUnanswered` = false : si la *cible de transfert* ne répond pas à l’appel transféré, il suit les paramètres de transfert et de non-réponse de la *cible de transfert*.
- `disableForwardingAndUnanswered` = true : si la *cible de transfert* ne répond pas à l’appel transféré, la tentative de transfert se termine

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Le transfert vous permet de vous abonner aux événements `transferStateChanged` et `transferRequested`. L’événement `transferRequsted` provient de l’instance de `call`, et l’événement `transferStateChanged` et les informations `state` et `error` du transfert proviennent de l’instance de `transfer`

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

Le transféré peut accepter ou rejeter la demande de transfert lancée par le transférant dans l’événement `transferRequested` via `accept()` ou `reject()` dans `transferRequestedEventArgs`. Vous pouvez accéder aux informations de `targetParticipant`, et aux méthodes `accept` et `reject` dans `transferRequestedEventArgs`.

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

## <a name="eventing-model"></a>Modèle d’événement
Vous devez inspecter les valeurs actuelles et vous abonner afin de mettre à jour les événements pour les valeurs ultérieures.

### <a name="properties"></a>Propriétés

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Collections
```js
// Inspect current collection
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
