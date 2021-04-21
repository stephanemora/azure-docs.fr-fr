---
title: Utilisation de la bibliothèque Azure Communication Services Teams Embed pour iOS
description: Dans cette présentation, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour iOS.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 0a1dd8f69cb79e42e56ab44981820e31abf204e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803022"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../access-tokens.md)
- Suivez le démarrage rapide pour [prendre en main l’ajout de Teams Embed à votre application](../getting-started-with-teams-embed.md).

## <a name="teams-embed-events"></a>Événements Teams Embed

Ajoutez `MeetingUIClientDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientDelegate {

    private var meetingClient: MeetingUIClient?
```

Définissez `meetingUIClientDelegate` sur `self`.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    meetingClient?.meetingUIClientDelegate = self
}
```

Implémentez les fonctions `didUpdateCallState` et `didUpdateRemoteParticipantCount`.

```swift
    func meetingUIClient(didUpdateCallState callState: CallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticpantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticpantCount)")
    }
```

## <a name="assigning-avatars-for-users"></a>Attribution d’avatars aux utilisateurs

Ajoutez `MeetingUIClientIdentityProviderDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientIdentityProviderDelegate {

    private var meetingClient: MeetingUIClient?
```

Définissez `MeetingUIClientIdentityProviderDelegate` sur `self` avant de rejoindre la réunion.

```swift
private func joinMeeting() {
    meetingClient?.meetingUIClientIdentityProviderDelegate = self
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")

    meetingClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Mappez chaque `userMri` avec l’avatar correspondant.

```swift
    func avatarFor(userIdentifier: String, completionHandler: @escaping (UIImage?) -> Void) {
        if (userIdentifier.starts(with: "8:teamsvisitor:")) {
            // Anonymous teams user will start with prefix 8:teamsvistor:
            let image = UIImage (named: "avatarPink")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:orgid:")) {
            // OrgID user will start with prefix 8:orgid:
            let image = UIImage (named: "avatarDoctor")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:acs:")) {
            // ACS user will start with prefix 8:acs:
            let image = UIImage (named: "avatarGreen")
            completionHandler(image!)
        }
        else {
            completionHandler(nil)
        }
}
```