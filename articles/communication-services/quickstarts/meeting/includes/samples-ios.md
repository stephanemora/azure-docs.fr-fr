---
title: Utilisation de la bibliothèque Azure Communication Services Teams Embed pour iOS
description: Dans cette présentation, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour iOS.
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 246c62ec25a788c7767da0c8fdf23b42db321f0b
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215136"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- Un `User Access Token` pour activer le client d’appel. Pour en savoir plus, consultez [Comment obtenir un `User Access Token`](../../access-tokens.md)
- Suivez le démarrage rapide pour [prendre en main l’ajout de Teams Embed à votre application](../getting-started-with-teams-embed.md).

## <a name="joining-a-group-call"></a>Rejoindre un appel de groupe

L’appel de groupe peut être rejoint en fournissant `MeetingUIClientGroupCallLocator` et `MeetingUIClientGroupCallJoinOptions` à l'API `meetingUIClient?.join`. L’appel de groupe ne sonnera pas pour les autres participants. L’utilisateur va rejoindre l’appel en mode silencieux.

Créez un bouton dans le rappel `viewDidLoad` du fichier **ViewController.swift**.

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let joinGroupCallButton = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
        joinGroupCallButton.backgroundColor = .black
        joinGroupCallButton.setTitle("Join Group Call", for: .normal)
        joinGroupCallButton.addTarget(self, action: #selector(joinGroupCallTapped), for: .touchUpInside)
        
        joinGroupCallButton.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(joinGroupCallButton)
        joinGroupCallButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        joinGroupCallButton.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }
```

Créez une prise pour le bouton dans **ViewController.swift** sur la classe `ViewController`.

```swift
@IBAction func joinGroupCallTapped(_ sender: UIButton) {
    joinGroupCall()
}

private func joinGroupCall() {
    // Add join meeting logic
}
```

La configuration du client et la fourniture du jeton s’effectuent de la même façon que pour l’API d’accès à la réunion, qui est décrite dans le [guide de démarrage rapide](../getting-started-with-teams-embed.md). 

La méthode `joinGroupCall` est définie en tant qu’action qui sera exécutée lors d’un appui sur le bouton *Rejoindre l’appel de groupe*.
Créez un `MeetingUIClientGroupCallLocator` et configurez les options d’accès à l’aide du `MeetingUIClientGroupCallJoinOptions`.
Remarque pour remplacer `<GROUP_ID>` par une chaîne UUID. La chaîne d’ID de groupe doit être au format GUID ou UUID.
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: <GROUP_ID>)
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall = meetingUIClientCall
            } else {
                print("Join meeting failed: \(error!)")
            }
        }
    })
}
```

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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
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

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-call"></a>Apportez votre propre identité de l’application aux participants dans l’appel.

L’application peut attribuer les valeurs d’identité de ses utilisateurs aux participants à l’appel ou à la réunion et remplacer les valeurs par défaut. Ces valeurs comprennent l’avatar, le nom et le sous-titre.  

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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
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
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Recevez des informations sur les actions des utilisateurs dans l’interface utilisateur et ajoutez vos propres fonctionnalités personnalisées.

### <a name="call-screen"></a>Écran d’appel 
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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: false, enableCallRosterDelegate: false)
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
### <a name="call-roster"></a>Liste d’appels 
Les méthodes de délégué `MeetingUIClientCallRosterDelegate` sont appelées à la suite d’actions de l’utilisateur sur un participant dans la liste.
Lorsque vous vous joignez à l’appel ou à la réunion, définissez la propriété des options de participation `enableCallRosterDelegate` sur `true`.
En définissant cette propriété, vous activez les options de plaque nominative dans le profil du participant distant et vous activez le délégué `MeetingUIClientCallRosterDelegate`.

Ajoutez `MeetingUIClientCallRosterDelegate` à votre classe.

```swift
class ViewController: UIViewController, MeetingUIClientCallRosterDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Définissez `self.meetingUIClientCall?.meetingUIClientCallRosterDelegate` sur `self` après avoir rejoint l’appel ou après que la réunion a démarré avec succès.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallRosterDelegate = self
            }
        }
    })
}
```

Ajoutez et implémentez la méthode de protocole `onCallParticipantCellTapped` et mappez chaque `identifier` à l’utilisateur participant à l’appel qui correspond.
Cette méthode est appelée par un simple clic sur un participant dans la liste d’appels.

```swift
func onCallParticipantCellTapped(identifier: CommunicationIdentifier) {
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

L’expérience utilisateur dans le Kit de développement logiciel (SDK) peut être personnalisée en fournissant des icônes spécifiques à l’application. 

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
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true, enableCallRosterDelegate: false)
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
        print("Unmute call failed: \(error!)")
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

// Lower the hand of current user for an active call.
// public func lowerHand(completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hang up the call or leave the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)
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
