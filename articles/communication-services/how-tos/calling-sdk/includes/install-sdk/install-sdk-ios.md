---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0ca40e760e34be8ef4299e042949f7a36b54f36f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838481"
---
## <a name="set-up-your-system"></a>Configurer votre système

### <a name="create-the-xcode-project"></a>Créer le projet Xcode

Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View App**. Ce guide de démarrage rapide utilise le [framework SwiftUI](https://developer.apple.com/xcode/swiftui/) ; vous devez donc définir **Swift** comme **langage** et **SwiftUI** comme **interface utilisateur**. 

Vous n’allez pas créer de tests unitaires ou de tests d’interface utilisateur au cours de ce guide de démarrage rapide. N’hésitez pas à décocher les cases **Include Unit Tests** (Inclure des tests unitaires) et **Include UI Tests** (Inclure des tests de l’interface utilisateur).

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-new-ios-project.png" alt-text="Capture d’écran montrant la fenêtre de création d’un projet dans Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installer le package et les dépendances avec CocoaPods

1. Créez un Podfile pour votre application, comme suit :

    ```
    platform :ios, '13.0'
    use_frameworks!
    target 'AzureCommunicationCallingSample' do
        pod 'AzureCommunicationCalling', '~> 1.0.0'
    end
    ```
2. Exécutez `pod install`.
3. Ouvrez `.xcworkspace` avec Xcode.

### <a name="request-access-to-the-microphone"></a>Demander l’accès au microphone

Pour accéder au microphone de l’appareil, vous devez mettre à jour la liste des propriétés d’informations de votre application avec `NSMicrophoneUsageDescription`. Vous affectez une valeur `string` comme valeur associée qui sera incluse dans la boîte de dialogue qu’affiche le système pour demander l’accès à l’utilisateur.

Cliquez avec le bouton droit sur l’entrée `Info.plist` de l’arborescence du projet, puis sélectionnez **Open As** > **Source Code**. Ajoutez les lignes suivantes à la section `<dict>` tout en haut, puis enregistrez le fichier.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Ouvrez le fichier *ContentView.swift* de votre projet et ajoutez une déclaration `import` en haut du fichier pour importer la bibliothèque `AzureCommunicationCalling`. En outre, importez `AVFoundation`. Vous en aurez besoin pour les demandes d’autorisations audio dans le code.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="initialize-callagent"></a>Initialiser CallAgent

Pour créer une instance de `CallAgent` à partir de `CallClient`, vous devez utiliser une méthode `callClient.createCallAgent` qui retourne de manière asynchrone un objet `CallAgent` après qu’il a été initialisé.

Pour créer un client d’appel, vous devez passer un objet `CommunicationTokenCredential`.

```swift
import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Transmettez l’objet `CommunicationTokenCredential` que vous avez créé à `CallClient` et définissez le nom d’affichage.

```swift
self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})
```