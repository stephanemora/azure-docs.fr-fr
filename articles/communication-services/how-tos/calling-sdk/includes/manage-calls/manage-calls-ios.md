---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c2deff9146d0f6ec28e3e37981bcb82d3e056947
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700475"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!NOTE]
> Quand l’application implémente les délégués d’événements, elle doit contenir une référence forte aux objets qui nécessitent des abonnements aux événements. Par exemple, lorsqu’un objet `RemoteParticipant` est retourné lors de l’appel de la méthode `call.addParticipant` et que l’application définit le délégué pour l’écoute du trafic sur `RemoteParticipantDelegate`, l’application doit contenir une référence forte à l’objet `RemoteParticipant`. Sinon, si cet objet est collecté, le délégué génère une exception irrécupérable quand le SDK Calling tente d’appeler l’objet.

## <a name="place-an-outgoing-call"></a>Passer un appel sortant

Pour créer et démarrer un appel, vous devez appeler l’une des API sur `CallAgent` et fournir l’identité Azure Communication Services d’un utilisateur que vous avez provisionné en utilisant le SDK de management des services de communication.

La création et le démarrage de l’appel sont synchrones. Vous recevrez une instance d’appel qui vous permet de vous abonner à tous les événements de l’appel.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Passer un appel 1:1 à un utilisateur ou un appel 1:n avec des utilisateurs et RTC

```swift
let callees = [CommunicationUser(identifier: 'UserId')]
self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully started outgoing call")
         self.call = call
     } else {
         print("Failed to start outgoing call")
     }
}
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Passer un appel 1:n avec des utilisateurs et un RTC
Pour passer l’appel RTC, vous devez spécifier un numéro de téléphone acquis avec les services Azure Communication Services.

```swift
let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [pstnCallee, callee], options: StartCallOptions()) { (groupCall, error) in
     if error == nil {
         print("Successfully started outgoing call to multiple participants")
         self.call = groupCall
     } else {
         print("Failed to start outgoing call to multiple participants")
     }
}
```

## <a name="join-a-group-call"></a>Rejoindre un appel de groupe
Pour rejoindre un appel, vous devez appeler l’une des API sur `CallAgent`.

```swift
let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string")!)
self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions()) { (call, error) in
    if error == nil {
        print("Successfully joined group call")
        self.call = call
    } else {
        print("Failed to join group call")
    }
}
```

## <a name="subscribe-to-an-incoming-call"></a>S’abonner à un appel entrant
Abonnez-vous à un événement d’appel entrant.

```swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didReceiveIncomingCall incomingcall: IncomingCall) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func incomingCall(_ incomingCall: IncomingCall, didEnd args: PropertyChangedEventArgs) {
        print("Incoming call was not answered")
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Acceptation d’un appel entrant
Pour accepter un appel, appelez la méthode `accept` sur un objet `IncomingCall`.

```swift
self.incomingCall!.accept(options: AcceptCallOptions()) { (call, error) in
   if (error == nil) {
       print("Successfully accepted incoming call")
       self.call = call
   } else {
       print("Failed to accept incoming call")
   }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
localVideoStreams = [LocalVideoStream]()
localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions.videoOptions = VideoOptions(localVideoStreams: localVideoStreams!)
if let incomingCall = self.incomingCall {
    incomingCall.accept(options: acceptCallOptions) { (call, error) in
        if error == nil {
            print("Incoming call accepted")
        } else {
            print("Failed to accept incoming call")
        }
    }
} else {
  print("No incoming call found to accept")
}
```

## <a name="perform-mid-call-operations"></a>Effectuer des opérations durant l’appel

Vous pouvez effectuer différentes opérations lors d’un appel pour gérer les paramètres liés à la vidéo et à l’audio.

### <a name="mute-and-unmute"></a>Activer et désactiver le son

Pour activer ou désactiver le son du point de terminaison local, vous pouvez utiliser les API asynchrones `mute` et `unmute`.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}
```

Utilisez le code suivant pour désactiver le point de terminaison local de manière asynchrone.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

## <a name="manage-remote-participants"></a>Gérer les participants distants

Tous les participants distants sont représentés par le type `RemoteParticipant` et sont disponibles via la collection `remoteParticipants` sur une instance d’appel.

### <a name="list-participants-in-a-call"></a>Répertorier les participants à un appel

```swift
call.remoteParticipants
```

### <a name="add-a-participant-to-a-call"></a>Ajouter un participant à un appel

Pour ajouter un participant à un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler `addParticipant`. Cette commande retourne une instance de participant distant de façon synchrone.

```swift
let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))
```

### <a name="remove-a-participant-from-a-call"></a>Supprimer un participant d’un appel
Pour supprimer un participant d’un appel (un utilisateur ou un numéro de téléphone), vous pouvez appeler l’API `removeParticipant`. Cela est résolu de façon asynchrone.

```swift
call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}
```

### <a name="get-remote-participant-properties"></a>Obtenir les propriétés du participant distant

```swift
// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identifier

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]
```