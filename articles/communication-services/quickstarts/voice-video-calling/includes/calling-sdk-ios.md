---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: fa7fd73a7d8019919a89dd9e9522b7389dc9c18f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930654"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../access-tokens.md)
- Facultatif : Suivez le démarrage rapide pour [prendre en main l’ajout de l’appel à votre application](../getting-started-with-calling.md)

## <a name="setting-up"></a>Configuration

### <a name="creating-the-xcode-project"></a>Création du projet Xcode

Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View App**. Ce démarrage rapide utilise le [framework SwiftUI](https://developer.apple.com/xcode/swiftui/) ; vous devez donc définir **Swift** comme **langage** et **SwiftUI** comme **interface utilisateur**. Vous n’allez pas créer de tests unitaires ou de tests d’interface utilisateur au cours de ce guide de démarrage rapide. N’hésitez pas à désactiver **Include Unit Tests** et **Include UI Tests**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Capture d’écran représentant la fenêtre de création de projet dans Xcode.":::

### <a name="install-the-package"></a>Installer le package

Ajoutez la bibliothèque de client Appel Azure Communication Services et ses dépendances (AzureCore.framework et AzureCommunication.framework) à votre projet.

> [!NOTE]
> Avec la publication du kit SDK AzureCommunicationCalling, vous trouverez un script bash `BuildAzurePackages.sh`. Le script, lors de l’exécution de `sh ./BuildAzurePackages.sh`, vous donnera le chemin des packages de framework générés qui devront être importés dans l’exemple d’application à l’étape suivante. Notez que vous devrez configurer les outils en ligne de commande Xcode si vous ne l’avez pas fait avant d’exécuter le script : Démarrez Xcode et sélectionnez « Preferences -> Locations ». Choisissez votre version de Xcode pour les outils en ligne de commande.

1. Téléchargez la bibliothèque de client Appel Communication Services pour iOS.
2. Dans Xcode, cliquez sur votre fichier projet et sélectionnez la cible de build pour ouvrir l’éditeur de paramètres du projet.
3. Sous l’onglet **General**, accédez à la section **Frameworks, Libraries, and Embedded Content** (Frameworks, bibliothèques et contenu incorporé), puis cliquez sur l’icône **« + »** .
4. En bas à gauche de la boîte de dialogue, choisissez **Add Files** (Ajouter des fichiers) et accédez au répertoire **AzureCommunicationCalling.framework** du package de la bibliothèque de client décompressé.
    1. Répétez la dernière étape pour ajouter **AzureCore.framework** et **AzureCommunication.framework**.
5. Ouvrez l’onglet **Build Settings** (Paramètres de build) de l’éditeur de paramètres du projet, puis faites défiler jusqu’à la section **Search Paths** (Chemins de recherche). Ajoutez une nouvelle entrée **Framework Search Paths** pour le répertoire contenant **AzureCommunicationCalling.framework**.
    1. Ajoutez une autre entrée Framework Search Paths pointant vers le dossier qui contient les dépendances.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Capture d’écran montrant la mise à jour des chemins de recherche de framework dans XCode.":::

### <a name="request-access-to-the-microphone"></a>Demander l’accès au microphone

Pour accéder au microphone de l’appareil, vous devez mettre à jour la liste des propriétés d’informations de votre application avec un `NSMicrophoneUsageDescription`. Vous affectez une valeur `string` comme valeur associée qui sera incluse dans la boîte de dialogue affichée par le système pour demander l’accès à l’utilisateur.

Cliquez avec le bouton droit sur l’entrée `Info.plist` de l’arborescence du projet, puis sélectionnez **Open As** > **Source Code**. Ajoutez les lignes suivantes dans la section `<dict>` tout en haut, puis enregistrez le fichier.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ouvrez le fichier **ContentView.swift** de votre projet et ajoutez une déclaration `import` en haut du fichier pour importer l’`AzureCommunicationCalling library`. Importez également `AVFoundation`. Nous en aurons besoin pour la demande d’autorisation audio dans le code.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client Appel Azure Communication Services pour iOS.


| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient est le point d’entrée principal de la bibliothèque de client Appel.|
| ACSCallAgent | ACSCallAgent sert à démarrer et à gérer les appels. |
| CommunicationUserCredential | CommunicationUserCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier sert à représenter l’identité de l’utilisateur qui peut être l’une des suivantes : CommunicationUser/PhoneNumber/CallingApplication. |

> [!NOTE]
> Lors de l’implémentation des délégués d’événements, l’application doit contenir une référence forte aux objets qui requièrent des abonnements aux événements. Par exemple, lorsqu’un objet `ACSRemoteParticipant` est retourné lors de l’appel de la méthode `call.addParticipant` et que l’application définit le délégué pour l’écoute du trafic sur `ACSRemoteParticipantDelegate`, l’application doit contenir une référence forte à l’objet `ACSRemoteParticipant`. Sinon, si cet objet est collecté, le délégué générera une exception irrécupérable lorsque le kit de développement logiciel (SDK) appelant tente d’appeler l’objet.

## <a name="initialize-the-acscallagent"></a>Initialiser ACSCallAgent

Pour créer une instance de `ACSCallAgent` à partir d’`ACSCallClient`, vous devez utiliser la méthode `callClient.createCallAgent` qui retourne de manière asynchrone un objet `ACSCallAgent` une fois qu’elle est initialisée

Pour créer un client d’appel, vous devez passer un objet `CommunicationUserCredential`.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Passe l’objet CommunicationUserCredential créé ci-dessus à ACSCallClient

```swift

callClient = ACSCallClient()
callClient?.createCallAgent(userCredential!,
    withCompletionHandler: { (callAgent, error) in
        if error != nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Passer un appel sortant

Pour créer et démarrer un appel, vous devez appeler l’une des API sur `ACSCallAgent` et fournir l’identité Azure Communication Services d’un utilisateur que vous avez configuré à l’aide de la bibliothèque cliente de management des services de communication.

La création et le démarrage de l’appel sont synchrones. Vous recevrez une instance d’appel qui vous permet de vous abonner à tous les événements de l’appel.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Passer un appel 1:1 à un utilisateur ou un appel 1:n avec des utilisateurs et RTC

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.CallingApp.callAgent.call(participants: callees, options: ACSStartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Passer un appel 1:n avec des utilisateurs et un RTC
Pour passer l’appel RTC, vous devez spécifier le numéro de téléphone acquis avec les services Azure Communication Services
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.CallingApp.callAgent.call(participants: [pstnCallee, callee], options: ACSStartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Passer un appel 1:1 avec vidéo
Pour obtenir une instance du gestionnaire de périphériques, consultez [ici](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(camera)
let videoOptions = ACSVideoOptions(localVideoStream)

let startCallOptions = ACSStartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call([callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Rejoindre un appel de groupe
Pour rejoindre un appel, vous devez appeler l’une des API sur *CallAgent*

```swift

let groupCallContext = ACSGroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: ACSJoinCallOptions())

```

## <a name="push-notification"></a>Notification Push

La notification Push mobile est la notification contextuelle que vous recevez sur un appareil mobile. Pour l’appel, nous utilisons des notifications Push VoIP (Voice over Internet Protocol). Nous vous offrirons la possibilité de vous inscrire et vous désinscrire aux notifications Push, ainsi que de les gérer.

### <a name="prerequisite"></a>Prérequis

- Étape 1 : Xcode -> Signature et fonctionnalités -> Ajouter une fonctionnalité -> « Notifications Push »
- Étape 2 : Xcode -> Signature et fonctionnalités -> Ajouter une fonctionnalité -> « Background Modes » (Modes d’arrière-plan)
- Étape 3 : « Background Modes » (Modes d’arrière-plan) -> sélectionner « Voix sur IP » et « Remote notifications » (Notifications distantes)

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Capture d’écran montrant comment ajouter des fonctionnalités dans Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Inscription aux notifications Push

Pour s’inscrire aux notifications Push, appelez registerPushNotification() sur une instance *CallAgent* avec un jeton d’inscription d’appareil.

L’inscription à la notification push doit être appelée après l’initialisation réussie. Lorsque l’objet `callAgent` est détruit, `logout` est appelé, ce qui annule automatiquement l’enregistrement des notifications push.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken,
                withCompletionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Gestion des notifications Push
Pour recevoir les notifications Push d’appels entrants, appelez *handlePushNotification()* sur une instance *CallAgent* avec une charge utile de dictionnaire.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(dictionaryPayload, withCompletionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Désinscription aux notifications Push

Les applications peuvent se désinscrire des notifications Push à tout moment. Appelez simplement la méthode `unRegisterPushNotification` sur *CallAgent*.
> [!NOTE]
> Les applications ne sont pas automatiquement désinscrites de la notification Push lors de la déconnexion.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Opérations durant l’appel

Vous pouvez effectuer différentes opérations lors d’un appel pour gérer les paramètres liés à la vidéo et à l’audio.

### <a name="mute-and-unmute"></a>Activer et désactiver le son

Pour activer ou désactiver le son du point de terminaison local, vous pouvez utiliser les API asynchrones `mute` et `unmute`  :

```swift
call.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

[Asynchrone] Réactivation locale du son

```swift
call.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Démarrer et arrêter l’envoi d’une vidéo locale

Pour commencer à envoyer une vidéo locale à d’autres participants de l’appel, utilisez l’API `startVideo` et transmettez `localVideoStream` avec `camera`

```swift

let firstCamera: ACSVideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(firstCamera)

call.startVideo(localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Une fois que vous avez commencé à envoyer de la vidéo, l’instance de `ACSLocalVideoStream` est ajoutée à la collection `localVideoStreams` sur une instance d’appel :

```swift

call.localVideoStreams[0]

```

[Asynchrone] Pour arrêter la vidéo locale, transmettez le `localVideoStream` retourné depuis l’appel de `call.startVideo` :

```swift

call.stopVideo(localVideoStream,{ (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    }
    else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Gestion des participants distants

Tous les participants distants sont représentés par le type `ACSRemoteParticipant` et sont disponibles via la collection `remoteParticipants` sur une instance d’appel :

### <a name="list-participants-in-a-call"></a>Lister les participants d’un appel

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Propriétés du participant distant

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Ajouter un participant à un appel

Pour ajouter un participant à un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler `addParticipant`. Cette opération retourne une instance de participant distant de façon synchrone .

```swift

let remoteParticipantAdded: ACSRemoteParticipant = call.addParticipant(CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Supprimer un participant d’un appel
Pour supprimer un participant d’un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler l’API `removeParticipant`. Cela est résolu de façon asynchrone.

```swift

call!.remove(remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Afficher les flux vidéo des participants distants

Les participants distants peuvent lancer un partage vidéo ou d’écran pendant un appel.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Gérer les flux de partage d’écran/vidéo de participants distants

Pour répertorier les flux de participants distants, examinez les collections `videoStreams` :

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Propriétés du flux vidéo distant

```swift

var type: ACSMediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Afficher le flux des participants distants

Pour commencer à afficher les flux des participants distants :

```swift

let renderer: ACSRenderer? = ACSRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: ACSRendererView? = renderer?.createView(ACSRenderingOptions(ACSScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(ACSScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Méthodes et propriétés du renderer vidéo à distance

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
```

## <a name="device-management"></a>Gestion des appareils

`DeviceManager` permet d’énumérer les périphériques locaux qui peuvent être utilisés dans un appel pour transmettre des flux audio/vidéo. Il vous permet également de demander l’autorisation à un utilisateur d’accéder au microphone/à la caméra. Vous pouvez accéder à `deviceManager` sur l’objet `callClient`  :

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Énumérer les appareils locaux

Pour accéder aux appareils locaux, vous pouvez utiliser les méthodes d’énumération sur le Gestionnaire d’appareils. L’énumération est une action synchrone.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Définir le microphone/le haut-parleur par défaut

Le gestionnaire d’appareils vous permet de définir un appareil par défaut qui sera utilisé lors du démarrage d’un appel. Si les valeurs par défaut de la pile ne sont pas définies, Azure Communication Services bascule vers les valeurs par défaut du système d’exploitation.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(ACSAudioDeviceInfo())
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeakers(ACSAudioDeviceInfo())
```

### <a name="local-camera-preview"></a>Aperçu de la caméra locale

Vous pouvez utiliser `ACSRenderer` pour commencer à afficher un flux à partir de votre caméra locale. Ce flux n’est pas envoyé à d’autres participants ; il s’agit d’un flux d’aperçu local. Il s’agit d’une action asynchrone.

```swift

let camera: ACSVideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: ACSLocalVideoStream = ACSLocalVideoStream(camera)
let renderer: ACSRenderer = ACSRenderer(localVideoStream: localVideoStream)
self.view = renderer!.createView(ACSRenderingOptions())

```

### <a name="local-camera-preview-properties"></a>Propriétés de l’aperçu de la caméra locale

Le renderer a un ensemble de propriétés et de méthodes qui vous permettent de contrôler le rendu :

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = ACSRenderer(localVideoStream:localVideoStream)
let remoteRenderer = ACSRenderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view with rendering options
localRenderer.createView(options:ACSRenderingOptions())
// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Modèle d’événement

Vous pouvez vous abonner à la plupart des propriétés et des collections pour être averti en cas de modifications de valeurs.

### <a name="properties"></a>Propriétés
Pour vous abonner à des événements `property changed` :

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: ACSCall!,
                               _ args: ACSPropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Collections
Pour vous abonner à des événements `collection updated` :

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: ACSCall!,
                                       _ args: ACSLocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
