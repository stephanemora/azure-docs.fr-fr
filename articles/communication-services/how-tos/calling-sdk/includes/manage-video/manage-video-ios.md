---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 4c95736394cd112daa48f3bfa6a47ae4ba0f147d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700468"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="manage-devices"></a>Gérer des unités
Pour commencer à utiliser de la vidéo avec les appels, vous devez savoir comment gérer les appareils. Les appareils vous permettent de contrôler ce que transmet l’audio et la vidéo à l’appel.

`DeviceManager` permet d’énumérer les appareils locaux qui peuvent être utilisés dans un appel pour transmettre des flux audio ou vidéo. Il vous permet également de demander l’autorisation à un utilisateur d’accéder à un microphone ou à une caméra. Vous pouvez accéder à `deviceManager` sur l’objet `callClient`.

```swift
self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Énumérer les appareils locaux

Pour accéder aux appareils locaux, vous pouvez utiliser les méthodes d’énumération sur le gestionnaire d’appareils. L’énumération est une action synchrone.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="get-a-local-camera-preview"></a>Obtenir l’aperçu de la caméra locale

Vous pouvez utiliser `Renderer` pour commencer à afficher un flux à partir de votre caméra locale. Ce flux n’est pas envoyé à d’autres participants ; il s’agit d’un flux d’aperçu local. Cette action est asynchrone.

```swift
let camera: VideoDeviceInfo = self.deviceManager!.cameras.first!
let localVideoStream = LocalVideoStream(camera: camera)
let localRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream)
self.view = try! localRenderer.createView()
```

### <a name="get-local-camera-preview-properties"></a>Obtenir les propriétés de l’aperçu de la caméra locale

Le renderer a un ensemble de propriétés et de méthodes qui vous permettent de contrôler le rendu.

```swift
// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = VideoStreamRenderer(localVideoStream:localVideoStream)
let remoteRenderer = VideoStreamRenderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [VideoStreamRendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer!.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()
```

## <a name="place-a-11-call-with-video"></a>Passer un appel 1:1 avec vidéo
Pour obtenir une instance du gestionnaire d’appareils, consultez la section relative à la [gestion des appareils](#manage-devices).

```swift
let firstCamera = self.deviceManager!.cameras.first
self.localVideoStreams = [LocalVideoStream]()
self.localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let videoOptions = VideoOptions(localVideoStreams: self.localVideoStreams!)

let startCallOptions = StartCallOptions()
startCallOptions.videoOptions = videoOptions

let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [callee], options: startCallOptions) { (call, error) in
    if error == nil {
        print("Successfully started outgoing video call")
        self.call = call
    } else {
        print("Failed to start outgoing video call")
    }
}
```

## <a name="render-remote-participant-video-streams"></a>Afficher les flux vidéo des participants distants

Les participants distants peuvent lancer un partage vidéo ou d’écran pendant un appel.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Gérer les flux de partage vidéo ou de partage d’écran de participants distants

Pour lister les flux de participants distants, examinez les collections `videoStreams`.

```swift
var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]
```

### <a name="get-remote-video-stream-properties"></a>Obtenir les propriétés du flux vidéo distant

```swift
var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'
var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available
var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream
```

### <a name="render-remote-participant-streams"></a>Afficher les flux des participants distants

Pour commencer à afficher les flux des participants distants, utilisez le code suivant.

```swift
let renderer = VideoStreamRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView = renderer?.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)
```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Obtenir les méthodes et propriétés du renderer vidéo à distance

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```