---
title: Démarrage rapide – Ajouter la participation à une réunion Microsoft Teams à une application iOS avec Azure Communication Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser la bibliothèque Azure Communication Services Teams Embed pour iOS.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5c2f53138d6f716d2917cff831e9b86c40b77a00
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111546366"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment rejoindre une réunion Microsoft Teams en utilisant la bibliothèque Azure Communication Services Teams Embed pour iOS.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, vous aurez besoin de ce qui suit :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un Mac exécutant [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), ainsi qu’un certificat de développeur valide installé dans votre trousseau
- Une ressource Communication Services déployée. [Créer une ressource Communication Services](../../create-communication-resource.md)
- Un [jeton d’accès utilisateur](../../access-tokens.md) pour votre service Azure Communication
- [CocoaPods](https://cocoapods.org/) installé pour votre environnement de build.

## <a name="setting-up"></a>Configuration

### <a name="creating-the-xcode-project"></a>Création du projet Xcode

Dans Xcode, créez un projet iOS et sélectionnez le modèle **App**. Nous utiliserons les plans conceptuels UIKit. Vous n’allez pas créer de tests au cours de ce guide démarrage rapide. N’hésitez pas à décocher **Inclure des tests**.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Capture d’écran montrant la sélection du modèle Nouveau projet dans Xcode.":::

Nommez le projet `TeamsEmbedGettingStarted`.

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Capture d’écran montrant les détails du nouveau projet dans Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installer le package et les dépendances avec CocoaPods

1. Créez un Podfile pour votre application :

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunicationCommon', '1.0.0'
end

azure_libs = [
'AzureCommunicationCommon',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Exécutez `pod install`.
3. Ouvrez le fichier `.xcworkspace` généré avec Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Demander l’accès au microphone, l’appareil photo, etc.

Pour accéder au matériel de l’appareil, mettez à jour la liste des propriétés d’informations de votre application. Définissez une valeur `string` comme valeur associée qui sera incluse dans la boîte de dialogue affichée par le système pour demander l’accès à l’utilisateur.

Cliquez avec le bouton droit sur l’entrée `Info.plist` de l’arborescence du projet, puis sélectionnez **Open As** > **Source Code**. Ajoutez les lignes suivantes dans la section `<dict>` tout en haut, puis enregistrez le fichier.

```xml
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
<key>NSPhotoLibraryUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Ajouter le framework Teams Embed

1. Téléchargez le `MicrosoftTeamsSDK`package iOS.
2. Créez un dossier `Frameworks` à la racine du projet. Ex. `\TeamsEmbedGettingStarted\Frameworks\`
3. Copiez les frameworks `TeamsAppSDK.framework` et `MeetingUIClient.framework` téléchargés et d’autres frameworks fournis dans le groupe de versions dans ce dossier.
4. Ajoutez ces frameworks à la cible de projet sous l’onglet général. Utilisez `Add Other` -> `Add Files...` pour accéder aux fichiers de framework et les ajouter.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Capture d’écran montrant les frameworks ajoutés dans Xcode.":::

5. Si ce n’est déjà fait, ajoutez `$(PROJECT_DIR)/Frameworks` à `Framework Search Paths` sous l’onglet des paramètres de build cible du projet. Pour rechercher le paramètre, remplacez le filtre `basic` par `all`. Vous pouvez également utiliser la barre de recherche à droite.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Capture d’écran montrant le chemin de recherche de framework dans Xcode.":::

### <a name="turn-off-bitcode"></a>Désactiver Bitcode

Définissez l’option `Enable Bitcode` sur `No` dans les paramètres `Build Settings` du projet. Pour trouver ce paramètre, vous devez modifier le filtre en remplaçant `Basic` par `All`. Vous pouvez également utiliser la barre de recherche située à droite.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Capture d’écran montrant l’option BitCode dans Xcode.":::


### <a name="turn-on-voice-over-ip-background-mode"></a>Activez le mode en arrière-plan Voix sur IP.

Sélectionnez la cible de votre application, puis cliquez sur l’onglet Fonctionnalités.

Activez `Background Modes` en cliquant sur `+ Capabilities` en haut, puis sélectionnez `Background Modes`.

Sélectionnez la case à cocher `Voice over IP`.

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Capture d’écran montrant VOIP activé dans Xcode.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Ajouter une référence de fenêtre à AppDelegate

Ouvrez le fichier **AppDelegate.swift** de votre projet et ajoutez une référence pour « fenêtre ».

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Ajouter un bouton à ViewController

Créez un bouton dans le rappel `viewDidLoad` du fichier **ViewController.swift**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Créez une prise pour le bouton dans **ViewController.swift**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ouvrez le fichier **ViewController.swift** de votre projet et ajoutez une déclaration `import` en haut du fichier pour importer `AzureCommunicationCommon library` et `MeetingUIClient`. 

```swift
import UIKit
import AzureCommunicationCommon
import MeetingUIClient
```

Remplacez l’implémentation de la classe `ViewController` par un simple bouton pour permettre à l’utilisateur de rejoindre une réunion. Dans ce guide de démarrage rapide, nous allons attacher une logique métier au bouton.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque Azure Communication Services Teams Embed :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | L’élément MeetingUIClient correspond au point d’entrée principal de la bibliothèque Teams Embed. |
| MeetingUIClientMeetingJoinOptions | Les éléments MeetingUIClientMeetingJoinOptions sont utilisés pour les options configurables telles que le nom d’affichage. |
| MeetingUIClientGroupCallJoinOptions | Les éléments MeetingUIClientMeetingJoinOptions sont utilisés pour les options configurables telles que le nom d’affichage. |
| MeetingUIClientTeamsMeetingLinkLocator | MeetingUIClientTeamsMeetingLinkLocator est utilisé pour définir l’URL de réunion pour rejoindre une réunion. |
| MeetingUIClientGroupCallLocator | MeetingUIClientGroupCallLocator est utilisé pour définir l’ID de groupe à rejoindre. |
| MeetingUIClientInCallScreenDelegate | MeetingUIClientInCallScreenDelegate est utilisé pour fournir des personnalisations sur l’écran d’appel principal dans l’interface utilisateur. |
| MeetingUIClientStagingScreenDelegate | MeetingUIClientStagingScreenDelegate est utilisé pour fournir des personnalisations sur l’écran d’appel intermédiaire dans l’interface utilisateur. |
| MeetingUIClientConnectingScreenDelegate | MeetingUIClientConnectingScreenDelegate est utilisé pour fournir des personnalisations sur l’écran d’appel de connexion dans l’interface utilisateur. |
| MeetingUIClientIconType | MeetingUIClientIconType est utilisé pour spécifier les icônes qui peuvent être remplacées par une icône spécifique d’application. |
| MeetingUIClientCall | MeetingUIClientCall décrit l’appel et fournit des API pour le contrôler. |
| MeetingUIClientCallState | L’élément MeetingUIClientCallState est utilisé pour signaler les changements d’état d’appel. Les options disponibles sont les suivantes : `connecting`, `waitingInLobby`, `connected` et `ended`. |
| MeetingUIClientUserRole | MeetingUIClientUserRole est utilisé pour définir les rôles utilisateur dans un appel de groupe. |
| MeetingUIClientAudioRoute | MeetingUIClientAudioRoute est utilisé pour des itinéraires audio locaux tels que `Earpiece` ou `SpeakerOn`. |
| MeetingUIClientLayoutMode | MeetingUIClientLayoutMode est utilisé pour permettre de sélectionner différents modes d’interface utilisateur dans l’appel. |
| MeetingUIClientAvatarSize | MeetingUIClientAvatarSize est utilisé pour notifier le type de taille d’avatar demandé par un délégué. |
| MeetingUIClientCallDelegate | L’élément MeetingUIClientDelegate est utilisé pour recevoir des événements, tels que des modifications d’état d’appel. |
| MeetingUIClientCallIdentityProviderDelegate | L’élément MeetingUIClientIdentityProviderDelegate est utilisé pour mapper les détails d’utilisateur sur les utilisateurs d’une réunion. |
| MeetingUIClientCallUserEventDelegate | L’élément MeetingUIClientUserEventDelegate fournit des informations sur les actions de l’utilisateur dans l’interface utilisateur. |

## <a name="create-and-authenticate-the-client"></a>Créer et authentifier le client

Initialisez une instance de `MeetingUIClient` avec un jeton d’accès utilisateur, qui nous permettra de rejoindre des réunions. Ajoutez le code suivant au rappel `viewDidLoad` dans **ViewController.swift** :

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Remplacez `<USER_ACCESS_TOKEN>` par un jeton d’accès utilisateur valide pour votre ressource. Consultez la documentation sur les [jetons d’accès utilisateur](../../access-tokens.md) si vous n’avez pas encore de jeton disponible.

### <a name="setup-token-refreshing"></a>Configurer l’actualisation du jeton

Créez une méthode `fetchTokenAsync`. Ajoutez ensuite votre logique `fetchToken` pour obtenir le jeton utilisateur.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Remplacez `<USER_ACCESS_TOKEN>` par un jeton d’accès utilisateur valide pour votre ressource.

## <a name="join-a-meeting"></a>Rejoindre une réunion

La méthode `join` est définie en tant qu’action exécutée lors d’un appui sur le bouton *Rejoindre la réunion*. Mettez à jour l’implémentation pour rejoindre une réunion avec `MeetingUIClient` :

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
            }
        }
    })
}
```
N’oubliez pas de remplacer `<MEETING URL>` par un lien de réunion Microsoft Teams.

Le gestionnaire d’achèvement retourne une erreur en cas d’échec de l’opération, ou `MeetingUIClientCall` en cas de réussite. Utilisez `MeetingUIClientCall` pour contrôler l’appel. 

### <a name="get-a-microsoft-teams-meeting-link"></a>Obtenir un lien de réunion Microsoft Teams

Un lien de réunion Microsoft Teams peut être récupéré par le biais des API Graph. Cette procédure est détaillée dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Le kit SDK Appel Communication Services accepte un lien de réunion Microsoft Teams complet. Ce lien est retourné comme faisant partie de la ressource `onlineMeeting`, accessible sous la [propriété `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Vous pouvez également récupérer les informations de réunion nécessaires à partir de l’URL **Rejoindre la réunion** dans l’invite de réunion Teams elle-même.

## <a name="run-the-code"></a>Exécuter le code

Vous pouvez générer et exécuter votre application sur un simulateur iOS en sélectionnant **Product** > **Run** ou en utilisant le raccourci clavier (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Apparence finale de l’application du guide de démarrage rapide":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Apparence finale une fois la réunion jointe":::

> [!NOTE]
> La première fois que vous rejoignez une réunion, le système vous invite à accorder l’accès au microphone. Dans une application de production, vous devez utiliser l’API `AVAudioSession` pour [vérifier l’état de l’autorisation](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) et mettre à jour le comportement de votre application de manière appropriée quand l’autorisation n’est pas accordée.

## <a name="add-localization-support-based-on-your-app"></a>Ajouter la prise en charge de la localisation en fonction de votre application

Le kit de développement logiciel (SDK) Microsoft Teams prend en charge plus de 100 chaînes et ressources. Le bundle de framework contient les langues de base et l’anglais. Les autres langues sont incluses dans le fichier `Localizations.zip` fourni avec le package.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Ajouter des localisations au kit de développement logiciel (SDK) en fonction de ce que votre application prend en charge

1. Déterminez le type de localisations prises en charge par votre application depuis Projet Xcode > Informations > Liste des localisations.
2. Décompressez le fichier Localizations.zip inclus dans le package.
3. Copiez les dossiers de localisation depuis le dossier décompressé en fonction de ce que votre application prend en charge à la racine du TeamsAppSDK.framework.


## <a name="sample-code"></a>Exemple de code

Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started).
