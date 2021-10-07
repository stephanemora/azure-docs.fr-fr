---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c8683ae7275c9cff9e3035a0ad4b0e1cdc6015d3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700471"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="device-management"></a>Gestion des périphériques
Pour commencer à utiliser de la vidéo avec les appels, vous devez savoir comment gérer les appareils. Les appareils vous permettent de contrôler ce que transmet l’audio et la vidéo à l’appel.

`DeviceManager` vous permet d’énumérer les appareils locaux utilisables dans un appel pour transmettre vos flux audio/vidéo. Il vous permet également de demander à un utilisateur l’autorisation d’accéder à son microphone et à sa caméra à l’aide de l’API de navigateur natif.

Vous pouvez accéder au `deviceManager` en appelant la méthode `callClient.getDeviceManager()`.
> [!WARNING]
> Actuellement, un objet `callAgent` doit être préalablement instancié pour avoir accès à DeviceManager

```java
Context appContext = this.getApplicationContext();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```

### <a name="enumerate-local-devices"></a>Énumérer les appareils locaux

Pour accéder aux appareils locaux, vous pouvez utiliser les méthodes d’énumération sur le gestionnaire d’appareils. L’énumération est une action synchrone.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="local-camera-preview"></a>Aperçu de la caméra locale

Vous pouvez utiliser `DeviceManager` et `Renderer` pour commencer à afficher le flux de votre caméra locale. Ce flux n’est pas envoyé à d’autres participants ; il s’agit d’un flux d’aperçu local. Cette action est asynchrone.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
videoOptions = new VideoOptions(localVideoStreams);

VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
VideoStreamRendererView uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="place-a-11-call-with-video-camera"></a>Passer un appel 1:1 avec une caméra vidéo
> [!WARNING]
> Actuellement, un seul flux vidéo local sortant est pris en charge. Pour passer un appel avec vidéo, vous devez énumérer les caméras locales à l’aide de l’API `deviceManager` `getCameras`.
Une fois la caméra sélectionnée, utilisez-la pour créer une instance `LocalVideoStream` et la transmettre à `videoOptions` en tant qu’élément dans le tableau `localVideoStream` vers une méthode `call`.
Une fois l’appel connecté, il commence automatiquement à envoyer un flux vidéo aux autres participants à partir de la caméra sélectionnée.

> [!NOTE]
> Pour des raisons de confidentialité, la vidéo ne sera pas partagée lors de l’appel si elle n’est pas visionnée localement.
Pour plus d’informations, consultez [Aperçu de la caméra locale](#local-camera-preview).
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager(appContext).get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;
VideoOptions videoOptions = new VideoOptions(localVideoStreams);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

## <a name="start-and-stop-sending-local-video"></a>Démarrer et arrêter l’envoi de vidéo locale

Pour démarrer une vidéo, vous devez énumérer les caméras à l’aide de l’API `getCameraList` sur l’objet `deviceManager`. Cela crée une nouvelle instance de `LocalVideoStream` transmettant la caméra souhaitée, et en la transmettant dans l’API `startVideo` comme argument :

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

Une fois que vous avez commencé à envoyer une vidéo, une instance `LocalVideoStream` est ajoutée à la collection `localVideoStreams` sur l’instance d’appel.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Pour arrêter la vidéo locale, transmettez l’instance `LocalVideoStream` disponible dans la collection `localVideoStreams` :

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

Vous pouvez basculer vers une autre caméra pendant l’envoi d’une vidéo en appelant `switchSource` sur une instance `LocalVideoStream` :
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="render-remote-participant-video-streams"></a>Afficher les flux vidéo des participants distants
Pour répertorier les flux vidéo et les flux de partage d’écran des participants distants, inspectez les collections `videoStreams` :
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Pour afficher le `RemoteVideoStream` d’un participant distant, vous devez vous abonner à un événement `OnVideoStreamsUpdated`.

Dans l’événement, la modification de la propriété `isAvailable` sur true indique que le participant distant envoie actuellement un flux. Une fois cette opération effectuée, créez une nouvelle instance d’un(e) `Renderer`, puis créez un nouveau/une nouvelle `RendererView` à l’aide de l’API `createView` asynchrone et joignez `view.target` n’importe où dans l’interface utilisateur de votre application.

Chaque fois que la disponibilité d’un flux distant change, vous pouvez choisir de détruire le convertisseur tout entier ou un `RendererView` spécifique, ou de les conserver, mais cela entraîne l’affichage d’une image vidéo vide.

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteParticipantStream, appContext);
VideoStreamRendererView uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.FIT));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Propriétés du flux vidéo distant
Un flux vidéo distant possède plusieurs propriétés

* `Id` - ID d’un flux vidéo distant
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` – peut être « Video » ou « ScreenSharing »
```java
MediaStreamType type = remoteVideoStream.getMediaStreamType();
```

* `isAvailable` – indique si le point de terminaison du participant distant envoie activement un flux
```java
boolean availability = remoteVideoStream.isAvailable();
```

### <a name="renderer-methods-and-properties"></a>Méthodes et propriétés du convertisseur
Objet du convertisseur suivant les API

* Créez une instance `VideoStreamRendererView` qui peut être jointe ultérieurement dans l’interface utilisateur de l’application pour afficher le flux vidéo distant.
```java
// Create a view for a video stream
VideoStreamRendererView.createView()
```
* Supprimez le convertisseur et tous les `VideoStreamRendererView` associés à ce convertisseur. À appeler lorsque vous avez supprimé toutes les vues associées de l’interface utilisateur.
```java
VideoStreamRenderer.dispose()
```

* `StreamSize` - taille (largeur/hauteur) d’un flux vidéo distant
```java
StreamSize renderStreamSize = VideoStreamRenderer.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```

### <a name="rendererview-methods-and-properties"></a>Méthodes et propriétés de RendererView
Lors de la création d’un `VideoStreamRendererView`, vous pouvez spécifier les propriétés `ScalingMode` et `mirrored` qui s’appliquent à cette vue : le mode de mise à l’échelle peut être « CROP » ou « FIT ».

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteVideoStream, appContext);
VideoStreamRendererView rendererView = remoteVideoRenderer.createView(new CreateViewOptions(ScalingMode.Fit));
```

Le RendererView créé peut alors être attaché à l’interface utilisateur de l’application à l’aide de l’extrait de code suivant :
```java
layout.addView(rendererView);
```

Vous pouvez modifier le mode de mise à l’échelle ultérieurement en appelant l’API `updateScalingMode` sur l’objet RendererView avec l’un des arguments suivants : « ScalingMode.CROP » ou « ScalingMode.FIT ».
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.CROP)
```