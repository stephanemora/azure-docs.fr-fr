---
title: Utilisation de la bibliothèque Azure Communication Services Teams Embed pour iOS
description: Dans cette présentation, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour iOS.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b6b3a2b45c3013170e8341228dd7b78b46881015
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107925334"
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

    private var meetingUIClient: MeetingUIClient?
```

Définissez `meetingUIClientDelegate` sur `self`.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    meetingUIClient?.meetingUIClientDelegate = self
}
```

Implémentez les fonctions `didUpdateCallState` et `didUpdateRemoteParticipantCount`.

```swift
    func meetingUIClient(didUpdateCallState callState: MeetingUIClientCallState) {
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

    private var meetingUIClient: MeetingUIClient?
```

Définissez `MeetingUIClientIdentityProviderDelegate` sur `self` avant de rejoindre la réunion.

```swift
private func joinMeeting() {
    meetingUIClient?.meetingUIClientIdentityProviderDelegate = self
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: <MEETING_URL>)
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Ajoutez et implémentez `avatarFor`, et mappez chaque `userMri` avec l'avatar correspondant.

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

Ajoutez d'autres méthodes de protocole MeetingUIClientIdentityProviderDelegate obligatoires à la classe. Elles peuvent être laissées avec une implémentation vide.
```swift
    func displayNameFor(userIdentifier: String, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(userIdentifier: String, completionHandler: @escaping (String?) -> Void) {
    }
```
