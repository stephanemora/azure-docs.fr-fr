---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 97e28db07e950fde42033ef57c76b2734b73f7cc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700472"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="device-management"></a>Gestion des périphériques
Pour commencer à utiliser de la vidéo avec les appels, vous devez savoir comment gérer les appareils. Les appareils vous permettent de contrôler ce que transmet l’audio et la vidéo à l’appel.

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
- L’énumération/sélection d’intervenants n’est pas prise en charge sur Android Chrome, iOS Safari et macOS Safari.

## <a name="start-and-stop-sending-local-video"></a>Démarrer et arrêter l’envoi de vidéo locale

Pour démarrer une vidéo, vous devez énumérer les caméras à l’aide de la méthode `getCameras` sur l’objet `deviceManager`. Créez ensuite une instance de `LocalVideoStream` avec la caméra souhaitée, puis passez l’objet `LocalVideoStream` dans la méthode `startVideo` d’un objet d’appel existant :

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

Il existe 4 méthodes où vous pouvez passer une instance de `localVideoStream` pour démarrer la vidéo dans un appel, `callAgent.startCall()`, `callAgent.join()`, `call.accept()` et `call.startVideo()`. Pour utiliser `call.stopVideo()`, vous devez passer la même instance de `localVideoStream` que vous avez passée à la méthode d’origine utilisée pour démarrer la vidéo.

Vous pouvez basculer vers une autre caméra pendant qu’une vidéo est envoyée en appelant `switchSource` sur une instance `localVideoStream` :

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

Si le périphérique vidéo spécifié est utilisé par un autre processus ou s’il est désactivé dans le système :
- Pendant un appel, si votre vidéo est désactivée et que vous démarrez la vidéo en utilisant `call.startVideo()`, cette méthode lève une exception `SourceUnavailableError` et `cameraStartFiled` est défini sur true.
- Un appel à la méthode `localVideoStream.switchSource()` va entraîner la définition de `cameraStartFailed` sur true.
Notre guide Diagnostics des appels fournit des informations supplémentaires sur la façon de diagnostiquer les problèmes liés aux appels.

## <a name="place-a-11-call-with-video-camera"></a>Passer un appel 1:1 avec une caméra vidéo

> [!IMPORTANT]
> Actuellement, un seul flux vidéo local sortant est pris en charge.

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

## <a name="videostreamrenderer-methods-and-properties"></a>Méthodes et propriétés de VideoStreamRenderer
Créez une instance `VideoStreamRendererView` qui peut être jointe dans l’interface utilisateur de l’application pour afficher le flux vidéo distant. Utilisez la méthode `createView()` asynchrone ; elle est résolue quand le flux est prêt à être affiché et retourne un objet avec une propriété `target` qui représente l’élément `video` pouvant être ajouté n’importe où dans l’arborescence DOM.

```js
await videoStreamRenderer.createView();
```

Supprimez `videoStreamRenderer` et toutes les instances `VideoStreamRendererView` associées :
```js
videoStreamRenderer.dispose();
```

## <a name="videostreamrendererview-methods-and-properties"></a>Méthodes et propriétés de VideoStreamRendererView

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
view.updateScalingMode('Crop');
```