---
title: Utilisation de la bibliothèque Azure Communication Services Teams Embed pour iOS
description: Dans cette présentation, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour iOS.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1a6c8d05da04dc0f32fb278baf946ea363010903
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111546507"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../access-tokens.md)
- Suivez le démarrage rapide pour [prendre en main l’ajout de Teams Embed à votre application](../getting-started-with-teams-embed.md).

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Capture d’événements relatifs à l’état des appels ou des réunions de Teams Embed

L’état de l’appel de groupe qui a été rejoint ou de la réunion peut être capturé à partir du délégué `MeetingUIClientCallDelegate`. L’état comprend les états de connexion, le nombre de participants et les modalités comme l’état du micro ou de la caméra.   

Ajoutez le délégué `MeetingUIClientCallDelegate` à votre classe et ajoutez les variables nécessaires.

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    //Rest of the UIViewController code
}
```

Définissez `self.meetingUIClientCall?.meetingUIClientCallDelegate` sur `self` après avoir rejoint l’appel ou après que la réunion a démarré avec succès.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

Implémentez les méthodes de protocole `MeetingUIClientCallDelegate` dont votre application a besoin et ajoutez des stubs pour celles qui ne sont pas nécessaires.

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
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }
    
    func onIsMutedChanged() {
    }
    
    func onIsSendingVideoChanged() {
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
    }
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>Apportez votre propre identité de l’application aux participants dans l’appel du Kit de développement logiciel (SDK).

L’application peut attribuer les valeurs d’identité de ses utilisateurs aux participants à l’appel ou à la réunion et remplacer les valeurs par défaut. Ces valeurs comprennent l’avatar, le nom, le sous-titre et le rôle.  

### <a name="assigning-avatars-for-call-participants"></a>Attribution d’avatars aux participants à l’appel

Ajoutez `MeetingUIClientCallIdentityProviderDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientCallIdentityProviderDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Définissez `self.meetingUIClientCall?.MeetingUIClientIdentityProviderDelegate` sur `self` après avoir rejoint l’appel ou après que la réunion a démarré avec succès.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallIdentityProviderDelegate = self
            }
        }
    })
}
```

Ajoutez et implémentez la méthode de protocole `avatarFor` et mappez chaque `identifier` à l’avatar correspondant.

```swift
    func avatarFor(identifier: CommunicationIdentifier, size: MeetingUIClientAvatarSize, completionHandler: @escaping (UIImage?) -> Void) {
        if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:teamsvisitor:")) {
                // Anonymous teams user will start with prefix 8:teamsvistor:
                let image = UIImage (named: "avatarPink")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:orgid:")) {
                // OrgID user will start with prefix 8:orgid:
                let image = UIImage (named: "avatarDoctor")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // ACS user will start with prefix 8:acs:
                let image = UIImage (named: "avatarGreen")
                completionHandler(image!)
            } else {
                completionHandler(nil)
            }
        } else {
            completionHandler(nil)
        }
    }

```

Ajoutez d’autres méthodes de protocole MeetingUIClientCallIdentityProviderDelegate obligatoires à la classe. Elles peuvent être laissées avec des implémentations vides.
```swift
    func displayNameFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func roleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (MeetingUIClientUserRole) -> Void) {
    }
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Utiliser le Kit de développement logiciel (SDK) Teams Embed et le Kit de développement logiciel (SDK) Appel d’Azure Communication Services dans la même application

Le Kit de développement logiciel (SDK) Teams Embed contient également le Kit de développement logiciel (SDK) Appel d’Azure Communication Services (ACS), ce qui permet d’utiliser les fonctionnalités des deux SDK dans la même application. Un seul Kit de développement logiciel (SDK) peut être initialisé et utilisé à la fois. Si les deux Kit de développement logiciel (SDK) sont initialisés et utilisés en même temps, cela se traduira par un comportement inattendu. 

Importez `TeamsAppSDK` pour accéder au Kit de développement logiciel (SDK) Appel d’Azure Communication Services à partir du Kit de développement logiciel (SDK) Teams Embed. 
```swift
import TeamsAppSDK
```

Déclarez des variables pour l’utilisation d’ACS.
```swift
class ViewController: UIViewController {

    private var callClient: CallClient?
    private var callAgent: CallAgent?
    private var call: Call?
    
    //Rest of the UIViewController code
}
    
```

L’initialisation s’effectue en créant un nouveau `CallClient`. Ajoutez la création à `viewDidLoad` ou à toute autre méthode.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    self.callClient = CallClient()
}
```
Utilisez toutes les API ACS comme elles sont décrites dans la documentation. L’utilisation de l’API n’est pas traitée dans cette documentation. 

Supprimez le Kit de développement logiciel (SDK) ACS et définissez `nil` sur ses variables une fois que son utilisation n’est plus nécessaire ou que l’application a besoin d’utiliser le Kit de développement logiciel (SDK) Teams Embed.
```swift
    public func disposeAcsSdk()
    {
        self.call = nil
        self.callAgent?.dispose()
        self.callClient?.dispose()
        self.callAgent = nil
        self.callClient = nil
    }

```

L’initialisation du Kit de développement logiciel (SDK) Teams Embed s’effectue également lors de la création de `MeetingUIClient`. Ajoutez la création à `viewDidLoad` ou à toute autre méthode.
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    do {
        let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
        let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
        meetingUIClient = MeetingUIClient(with: credential)
    }
    catch {
        print("Failed to create communication token credential")
    }
}

private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}

```
Utilisez les API du Kit de développement logiciel (SDK) Teams Embed comme elles sont décrites dans la documentation. L’utilisation de l’API n’est pas traitée dans cette documentation. 

Supprimez le Kit de développement logiciel (SDK) Teams Embed et définissez `nil` sur ses variables une fois que son utilisation n’est plus nécessaire ou que l’application a besoin d’utiliser le Kit de développement logiciel (SDK) ACS.
```swift
    private func disposeTeamsSdk() {
        self.meetingUIClient?.dispose(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("Dispose failed: \(error!)")
            } else {
                self.meetingUIClient = nil
                self.meetingUIClientCall = nil
            }
        })        
    }

```

La suppression du Kit de développement logiciel (SDK) Teams Embed n’est possible que s’il n’y a pas d’appels actifs. `meetingUIClient?.dispose` renvoie une erreur dans son gestionnaire d’achèvement s’il y a un appel actif. Raccrochez l’appel actif et appelez `self.disposeTeamsSdk()` s’il n’y a plus d’appel actif.
```swift
    
    private var shouldDispose: Bool = false

    public func endMeeting() {
        meetingUIClientCall?.hangUp(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("End meeting failed: \(error!)")
                // There are no active calls
                self.disposeTeamsSdk()
            } else {
                // Ending active call is in progress.
                self.shouldDispose = true;
            }
        })
    }

```

Implémentez la méthode de protocole `MeetingUIClientCallDelegate` `meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState)` pour obtenir la mise à jour de l’état de l’appel actif qui se termine et supprimez le Kit de développement logiciel (SDK) Teams Embed.
```swift
    func meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
        case .connected:
        case .waitingInLobby:
        case .ended:
            if (self.shouldDispose) {
                self.disposeTeamsSdk()
            }
        @unknown default:
            print("Unsupported state")
        }
    }
```

Ajoutez d’autres méthodes de protocole `MeetingUIClientCallDelegate` obligatoires à la classe.

```swift
    func meetingUIClientCall(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }

    func onIsMutedChanged() {
        print("Mute state changed to: \(meetingUIClientCall?.isMuted ?? false)")
    }
    
    func onIsSendingVideoChanged() {
        print("Sending video state changed to: \(meetingUIClientCall?.isSendingVideo ?? false)")
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
        print("Self participant raise hand status changed to: \(meetingUIClientCall?.isHandRaised ?? false)")
    }
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Recevez des informations sur les actions des utilisateurs dans l’interface utilisateur et ajoutez vos propres fonctionnalités personnalisées.

Les méthodes de délégué `MeetingUIClientCallUserEventDelegate` sont appelées à la suite d’actions de l’utilisateur dans le profil du participant distant.
Lorsque vous vous joignez à l’appel ou à la réunion, définissez la propriété des options de participation `enableNamePlateOptionsClickDelegate` sur `true`.
En définissant cette propriété, vous activez les options de plaque nominative dans le profil du participant distant et vous activez le délégué `MeetingUIClientCallUserEventDelegate`.

Ajoutez `MeetingUIClientCallUserEventDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientCallUserEventDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Définissez `self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate` sur `self` après avoir rejoint l’appel ou après que la réunion a démarré avec succès.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate = self
            }
        }
    })
}
```

Ajoutez et implémentez la méthode de protocole `onNamePlateOptionsClicked` et mappez chaque `identifier` à l’utilisateur participant à l’appel qui correspond.
Cette méthode est appelée en appuyant sur la vignette de l’utilisateur ou sur le texte du titre de l’utilisateur dans l’écran d’appel principal.

```swift
func onNamePlateOptionsClicked(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```

Ajoutez et implémentez la méthode de protocole `onParticipantViewLongPressed` et mappez chaque `identifier` à l’utilisateur participant à l’appel qui correspond.
Cette méthode est appelée en appuyant longuement sur la vignette de l’utilisateur dans l’écran d’appel principal.

```swift
func onParticipantViewLongPressed(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
## <a name="user-experience-customization"></a>Personnalisation de l’expérience utilisateur

L’expérience utilisateur dans le Kit de développement logiciel (SDK) peut être personnalisée en fournissant des icônes spécifiques à l’application ou en remplaçant des barres de contrôles d’appel. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>Personnaliser les icônes de l’interface utilisateur dans un appel ou une réunion

Les icônes affichées dans l’appel ou la réunion peuvent être personnalisées par le biais de la méthode `public func set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` exposée dans `MeetingUIClient`.
La liste des icônes qui peuvent être personnalisées est disponible dans `MeetingUIClientIconType`.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Après l’initialisation de meetingUIClient, définissez la configuration des icônes `meetingUIClient?.set(iconConfig: self.getIconConfig())` pour les icônes d’appel prises en charge dans `MeetingUIClientIconType`.

```swift
private func initMeetingUIClient() {
    meetingUIClient = MeetingUIClient(with: credential)
    meetingUIClient?.set(iconConfig: self.getIconConfig())
}

func getIconConfig() -> Dictionary<MeetingUIClientIconType, String> {
    var iconConfig = Dictionary<MeetingUIClientIconType, String>()
    iconConfig.updateValue("camera_fill", forKey: MeetingUIClientIconType.VideoOn)
    iconConfig.updateValue("camera_off", forKey: MeetingUIClientIconType.VideoOff)
    iconConfig.updateValue("microphone_fill", forKey: MeetingUIClientIconType.MicOn)
    iconConfig.updateValue("microphone_off", forKey: MeetingUIClientIconType.MicOff)
    iconConfig.updateValue("speaker_fill", forKey: MeetingUIClientIconType.Speaker)
    return iconConfig
}
```

### <a name="customize-main-call-screen"></a>Personnaliser l’écran d’appel principal

Le `MeetingUIClient` fournit la prise en charge permettant de personnaliser l’interface utilisateur de l’écran d’appel principal. Actuellement, il prend en charge la personnalisation de l’interface utilisateur à l’aide de méthodes de protocole `MeetingUIClientInCallScreenDelegate`.
Les actions de contrôle de l’écran d’appel sont exposées par le biais des méthodes présentes dans `MeetingUIClientCall`.

Ajoutez `MeetingUIClientInCallScreenDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientInCallScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Définissez `meetingUIClient?.meetingUIClientInCallScreenDelegate` sur `self` avant de rejoindre l’appel ou la réunion.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientInCallScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

Ajoutez et implémentez la méthode de protocole `provideControlTopBar` pour fournir la barre d’informations supérieure de l’écran d’appel principal.

```swift
func provideControlTopBar() -> UIView? {
    let topView = UIStackView.init()
    // add your customization here
    return topView
}
```

Ajoutez d’autres méthodes de protocole `MeetingUIClientInCallScreenDelegate` obligatoires à la classe. Elles peuvent être laissées avec des implémentations vides pour renvoyer une valeur nulle.
```swift
func provideControlBottomBar() -> UIView? {
    return nil
}

func provideScreenBackgroudColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-staging-call-screen"></a>Personnaliser l’écran d’appel intermédiaire

Le `MeetingUIClient` fournit la prise en charge permettant de personnaliser l’interface utilisateur de l’écran d’appel intermédiaire. Actuellement, il prend en charge la personnalisation de l’interface utilisateur à l’aide de méthodes de protocole `MeetingUIClientStagingScreenDelegate`.
Lorsque vous vous joignez à l’appel ou à la réunion, définissez la propriété des options de participation `enableCallStagingScreen` sur `true` pour afficher l’écran intermédiaire.

Ajoutez `MeetingUIClientStagingScreenDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientStagingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Définissez `meetingUIClient?.meetingUIClientStagingScreenDelegate` sur `self` avant de rejoindre l’appel ou la réunion.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientStagingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

Ajoutez et implémentez la méthode de protocole `provideJoinButtonCornerRadius` pour modifier l’apparence du bouton Rejoindre en lui donnant un aspect arrondi.

```swift
func provideJoinButtonCornerRadius() -> CGFloat {
    return 24
}
```

Ajoutez d’autres méthodes de protocole `MeetingUIClientStagingScreenDelegate` obligatoires à la classe. Elles peuvent être laissées avec des implémentations vides pour renvoyer une valeur nulle.
```swift
func provideJoinButtonBackgroundColor() -> UIColor? {
    return nil
}

func provideStagingScreenBackgroundColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-connecting-call-screen"></a>Personnaliser l’écran d’appel de connexion

Le `MeetingUIClient` fournit la prise en charge permettant de personnaliser l’interface utilisateur de l’écran d’appel de connexion. Actuellement, il prend en charge la personnalisation de l’interface utilisateur à l’aide de méthodes de protocole `MeetingUIClientConnectingScreenDelegate`.
Utilisez la méthode de configuration des icônes `set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` exposée dans `MeetingUIClient` pour modifier uniquement les icônes affichées et utiliser les fonctionnalités fournies par le `MeetingUIClient`.


Ajoutez `MeetingUIClientConnectingScreenDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientConnectingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Définissez `meetingUIClient?.meetingUIClientConnectingScreenDelegate` sur `self` avant de rejoindre l’appel ou la réunion.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientConnectingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

Ajoutez et implémentez la méthode de protocole `provideConnectingScreenBackgroundColor` pour modifier la couleur d’arrière-plan de l’écran de connexion.

```swift
func provideConnectingScreenBackgroundColor() -> UIColor?
    return 24
}
```

## <a name="perform-operations-with-the-call"></a>Effectuer des opérations pendant l’appel

Les actions de contrôle de l’appel sont exposées par le biais des méthodes présentes dans `MeetingUIClientCall`.
Ces méthodes sont utiles pour contrôler les actions d’appel si l’interface utilisateur a été personnalisée à l’aide des délégués de personnalisation `MeetingUIClient`.

Ajouter les variables nécessaires aux appels
```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Attribuez à la variable `self.meetingUIClientCall` la valeur `meetingUIClientCall` de la méthode `join` `completionHandler`.
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```
### <a name="mute-and-unmute"></a>Activer et désactiver le son

Appelez la méthode `mute` pour désactiver le micro pendant un appel actif, s’il en existe un.
Les changements d’état du micro sont notifiés dans la méthode `onIsMutedChanged` de `MeetingUIClientCallDelegate`.

```swift
// Mute the microphone for an active call.
public func mute(completionHandler: @escaping (Error?) -> Void)

    meetingUIClientCall?.mute { [weak self] (error) in
        if error != nil {
            print("Mute call failed: \(error!)")
        }
}
```

Appelez la méthode `unmute` pour réactiver le micro pendant un appel actif, s’il en existe un.

```swift
// Unmute the microphone for an active call.
public func unmute(completionHandler: @escaping (Error?) -> Void)

meetingUIClientCall?.unmute { [weak self] (error) in
    if error != nil {
        print("Mute call failed: \(error!)")
    }
}
```

### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>D’autres opérations sont disponibles dans la classe `MeetingUIClientCall`.

```swift
// Start the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Stop the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Set the preferred audio route in the call for self user.
public func setAudio(route: MeetingUIClientAudioRoute, completionHandler: @escaping (Error?) -> Void)

// Raise the hand of current user for an active call.
public func raiseHand(completionHandler: @escaping (Error?) -> Void)

// Lower the hand of user provided in the identifier for an active call.
// public func lowerHand(identifier: CommunicationIdentifier, completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hangs up the call or leaves the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)

// Set the user role for an active call.
public func setRoleFor(identifier: CommunicationIdentifier, userRole: MeetingUIClientUserRole, completionHandler: @escaping (Error?) -> Void)
```
