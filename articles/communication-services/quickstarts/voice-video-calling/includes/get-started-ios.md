---
title: 'Démarrage rapide : Ajouter l’appel à une application iOS à l’aide d’Azure Communication Services'
description: Dans ce guide de démarrage rapide, vous allez apprendre à utiliser la bibliothèque de client Appel Azure Communication Services pour iOS.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c67440453e5ca8395464369d75bfac418a564764
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944802"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment démarrer un appel à l’aide de la bibliothèque de client Appel Azure Communication Services pour iOS.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous devez disposer des prérequis suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un Mac exécutant [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), ainsi qu’un certificat de développeur valide installé dans votre trousseau
- Une ressource Communication Services déployée. [Créer une ressource Communication Services](../../create-communication-resource.md)
- Un [jeton d’accès utilisateur](../../access-tokens.md) pour votre service Azure Communication

## <a name="setting-up"></a>Configuration

### <a name="creating-the-xcode-project"></a>Création du projet Xcode

Dans Xcode, créez un projet iOS et sélectionnez le modèle **Single View App**. Ce tutoriel utilise le [framework SwiftUI](https://developer.apple.com/xcode/swiftui/) ; vous devez donc définir **Swift** comme **langage** et **SwiftUI** comme **interface utilisateur**. Vous n’allez pas créer de tests unitaires ou de tests d’interface utilisateur au cours de ce guide démarrage rapide. N’hésitez pas à désactiver **Include Unit Tests** et **Include UI Tests**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Capture d’écran montrant la fenêtre de création de projet dans Xcode.":::

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
    1. Ajoutez une autre entrée Framework Search Paths pointant vers le dossier contenant les dépendances.

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

Remplacez l’implémentation du struct `ContentView` par des contrôles d’interface utilisateur simples qui permettent à un utilisateur de lancer et de terminer un appel. Dans ce guide de démarrage rapide, nous allons attacher une logique métier à ces contrôles.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: ACSCallClient?
    @State var callAgent: ACSCallAgent?
    @State var call: ACSCall?

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

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client Azure Communication Services Calling :

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | CallClient est le point d’entrée principal de la bibliothèque de client Calling.|
| ACSCallAgent | CallAgent sert à démarrer et à gérer les appels. |
| CommunicationUserCredential | CommunicationUserCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier sert à représenter l’identité de l’utilisateur qui peut être l’une des suivantes : CommunicationUser/PhoneNumber/CallingApplication. |

## <a name="authenticate-the-client"></a>Authentifier le client

Initialisez une instance de `CallAgent` avec un jeton d’accès utilisateur qui nous permettra d’établir et de recevoir des appels. Ajoutez le code suivant au rappel `onAppear` dans **ContentView.swift** :

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = ACSCallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Vous devez remplacer `<USER ACCESS TOKEN>` par un jeton d’accès utilisateur valide pour votre ressource. Consultez la documentation sur les [jetons d’accès utilisateur](../../access-tokens.md) si vous n’avez pas encore de jeton disponible.

## <a name="start-a-call"></a>Démarrer un appel

La méthode `startCall` est définie en tant qu’action qui sera exécutée lors d’un appui sur le bouton *Start Call*. Mettez à jour l’implémentation pour démarrer un appel avec `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: ACSStartCallOptions())
        }
    }
}
```

Vous pouvez également utiliser les propriétés dans `ACSStartCallOptions` pour définir les options initiales de l’appel (cela permet de démarrer l’appel avec le microphone muet).

## <a name="end-a-call"></a>Terminer un appel

Implémentez la méthode `endCall` pour terminer l’appel en cours en cas d’appui sur le bouton *End Call*.

```swift
func endCall()
{    
    self.call!.hangup(ACSHangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Exécuter le code

Vous pouvez générer et exécuter votre application sur un simulateur iOS en sélectionnant **Product** > **Run** ou en utilisant le raccourci clavier (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Apparence finale de l’application du guide de démarrage rapide":::

Vous pouvez établir un appel VoIP sortant en fournissant un ID d’utilisateur dans le champ de texte et en appuyant sur le bouton **Start Call**. L’appel de `8:echo123` vous connecte à un bot d’écho, ce qui est parfait pour bien démarrer et vérifier que vos périphériques audio fonctionnent. 

> [!NOTE]
> La première fois que vous effectuez un appel, le système vous invite à accorder l’accès au microphone. Dans une application de production, vous devez utiliser l’API `AVAudioSession` [vérifier l’état de l’autorisation](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) et mettre à jour le comportement de votre application de manière appropriée lorsque l’autorisation n’est pas accordée.

## <a name="sample"></a>Exemple

Vous pouvez télécharger l’exemple d’application à partir de [GitHub](https://github.com/Azure/Communication/tree/master/samples/AzureCommunicationCalling/iOS/Swift).
