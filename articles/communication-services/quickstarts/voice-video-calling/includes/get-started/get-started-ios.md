---
title: 'Démarrage rapide : Ajouter l’appel à une application iOS à l’aide d’Azure Communication Services'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment utiliser le kit de développement logiciel (SDK) Azure Communication Services Calling pour iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 27d1271450715babeb94bfe929e54500c59aa664
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111560801"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment démarrer un appel à l’aide du kit de développement logiciel (SDK) Azure Communication Services Calling pour iOS.

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer des prérequis suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un Mac exécutant [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), ainsi qu’un certificat de développeur valide installé dans votre trousseau
- Une ressource Communication Services déployée. [Créer une ressource Communication Services](../../../create-communication-resource.md)
- Un [jeton d’accès utilisateur](../../../access-tokens.md) pour votre service Azure Communication

## <a name="setting-up"></a>Configuration

### <a name="creating-the-xcode-project"></a>Création du projet Xcode

Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View App**. Ce tutoriel utilise le [framework SwiftUI](https://developer.apple.com/xcode/swiftui/) ; vous devez donc définir **Swift** comme **langage** et **SwiftUI** comme **interface utilisateur**. Vous n’allez pas créer de tests au cours de ce guide démarrage rapide. N’hésitez pas à décocher **Inclure des tests**.

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Capture d’écran représentant la fenêtre Nouveau projet dans Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installer le package et les dépendances avec CocoaPods

1. Pour créer un Podfile pour votre application, ouvrez le terminal et accédez au dossier du projet, puis exécutez ```pod init```.
3. Ajoutez le code suivant au Podfile et enregistrez-le (assurez-vous que la cible correspond au nom de votre projet) :

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0'
   end
   ```

3. Exécutez `pod install`.
3. Ouvrez le `.xcworkspace` avec Xcode.

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

Remplacez l’implémentation du struct `ContentView` par des contrôles d’interface utilisateur simples qui permettent à un utilisateur de lancer et de terminer un appel. Dans ce guide de démarrage rapide, nous allons attacher une logique métier à ces contrôles.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Azure Communication Services Calling :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient est le point d’entrée principal du SDK Calling.|
| CallAgent | CallAgent sert à démarrer et à gérer les appels. |
| CommunicationTokenCredential | CommunicationTokenCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier sert à représenter l’identité de l’utilisateur qui peut être l’une des suivantes : CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Authentifier le client

Initialisez une instance de `CallAgent` avec un jeton d’accès utilisateur qui nous permettra d’établir et de recevoir des appels. Ajoutez le code suivant au rappel `onAppear` dans **ContentView.swift** :

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }
    else {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Vous devez remplacer `<USER ACCESS TOKEN>` par un jeton d’accès utilisateur valide pour votre ressource. Consultez la documentation sur les [jetons d’accès utilisateur](../../../access-tokens.md) si vous n’avez pas encore de jeton disponible.

## <a name="start-a-call"></a>Démarrer un appel

La méthode `startCall` est définie en tant qu’action qui sera exécutée lors d’un appui sur le bouton *Start Call*. Mettez à jour l’implémentation pour démarrer un appel avec `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
            self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
                if (error == nil) {
                    self.call = call
                } else {
                    print("Failed to get call object")
                }
            }
        }
    }
}
```

Vous pouvez également utiliser les propriétés dans `StartCallOptions` pour définir les options initiales de l’appel (cela permet de démarrer l’appel avec le microphone muet).

## <a name="end-a-call"></a>Terminer un appel

Implémentez la méthode `endCall` pour terminer l’appel en cours en cas d’appui sur le bouton *End Call*.

```swift
func endCall()
{    
    self.call!.hangUp(options: HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Exécuter le code

Vous pouvez générer et exécuter votre application sur un simulateur iOS en sélectionnant **Product** > **Run** ou en utilisant le raccourci clavier (&#8984;-R).

:::image type="content" source="../../media/ios/quick-start-make-call.png" alt-text="Apparence finale de l’application du guide de démarrage rapide":::

Vous pouvez établir un appel VoIP sortant en fournissant un ID d’utilisateur dans le champ de texte et en appuyant sur le bouton **Start Call**. L’appel de `8:echo123` vous connecte à un bot d’écho, ce qui est parfait pour bien démarrer et vérifier que vos périphériques audio fonctionnent. 

> [!NOTE]
> La première fois que vous effectuez un appel, le système vous invite à accorder l’accès au microphone. Dans une application de production, vous devez utiliser l’API `AVAudioSession` pour [vérifier l’état de l’autorisation](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) et mettre à jour le comportement de votre application de manière appropriée quand l’autorisation n’est pas accordée.

## <a name="sample-code"></a>Exemple de code

Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling).
