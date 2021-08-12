---
title: 'Démarrage rapide : Rejoindre une réunion Teams à partir d’une application iOS'
description: Dans ce tutoriel, vous allez découvrir comment rejoindre une réunion Teams à l’aide du kit de développement logiciel (SDK) Azure Communication Services Calling pour iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: c23512eeb044d07e845de2a24ab6afd3bd190101
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112536274"
---
Dans ce démarrage rapide, vous allez découvrir comment rejoindre une réunion Teams à l’aide du kit de développement logiciel (SDK) Azure Communication Services Calling pour iOS.

## <a name="prerequisites"></a>Prérequis

- Une [application iOS appelant Communication Services](../../getting-started-with-calling.md) qui fonctionne
- Un [déploiement de Teams](/deployoffice/teams-install)

Nous allons utiliser la version beta.12 du kit de développement logiciel (SDK) AzureCommunicationCalling pour ce guide de démarrage rapide. Nous devons donc mettre à jour le podfile et réinstaller les pods. 

Remplacez votre podfile par le code suivant et enregistrez la modification (assurez-vous que la cible correspond au nom de votre projet) :

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '1.0.0-beta.12'
   end
   ```
Supprimez votre dossier Pods, Podfile.lock et le fichier `.xcworkspace.`.

Exécutez `pod install` et ouvrez le `.xcworkspace` avec Xcode.

## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>Ajouter les contrôles d’interface utilisateur Teams et les activer

Remplacez le code dans ContentView.swift par l’extrait de code suivant. La zone de texte servira à entrer le contexte de la réunion Teams, tandis que le bouton permettra de rejoindre la réunion spécifiée :

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
                if error != nil {
                    self.message = "Failed to create CallAgent."
                    return
                } else {
                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink)
                self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) {(call, error) in
                    if (error == nil) {
                        self.call = call
                        self.callObserver = CallObserver(self)
                        self.call!.delegate = self.callObserver
                        self.message = "Teams meeting joined successfully"
                    } else {
                        print("Failed to get call object")
                        return
                    }
                }
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangUp(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hangup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func call(_ call: Call, didChangeRecordingState args: PropertyChangedEventArgs) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Obtenir le lien de réunion Teams

Le lien de réunion Teams peut être récupéré par le biais des API Graph. Cette procédure est détaillée dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Le kit SDK Communication Services Calling accepte un lien de réunion Teams complet. Ce lien est retourné dans le cadre de la ressource `onlineMeeting`, accessible sous la [propriété `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Vous pouvez également récupérer les informations de réunion requises à partir de l’URL **Rejoindre la réunion** disponible dans l’invitation à la réunion Teams.

## <a name="launch-the-app-and-join-teams-meeting"></a>Lancer l’application et rejoindre la réunion Teams

Vous pouvez générer et exécuter votre application sur un simulateur iOS en sélectionnant **Product** > **Run** ou en utilisant le raccourci clavier (&#8984;-R).

:::image type="content" source="../../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Capture d’écran montrant l’application terminée.":::

Insérez le contexte Teams dans la zone de texte et appuyez sur *Join Teams Meeting* pour rejoindre la réunion Teams à partir de votre application Communication Services.
