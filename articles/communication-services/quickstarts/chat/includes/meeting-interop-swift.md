---
title: Démarrage rapide - Participer à une réunion Teams
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 335e21057d0deaa68b4b791501e59aaa772eb49c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113113094"
---
Dans ce guide de démarrage rapide, vous allez découvrir comment rejoindre une conversation dans une réunion Teams à l’aide du kit SDK Conversation Azure Communication Services pour iOS.

## <a name="prerequisites"></a>Prérequis 

-  [Déploiement Teams](/deployoffice/teams-install). 
- Une [application d’appel](../../voice-video-calling/get-started-teams-interop.md) opérationnelle. 

## <a name="enable-teams-interoperability"></a>Activer l’interopérabilité de Teams 

Un utilisateur Communication Services qui rejoint une réunion Teams en tant qu’utilisateur invité ne peut accéder à la conversation de la réunion qu’après avoir rejoint l’appel de réunion Teams. Consultez la documentation [Interopérabilité de Teams](../../voice-video-calling/get-started-teams-interop.md) pour savoir comment ajouter un utilisateur Communication Services à un appel de réunion Teams.

Vous devez être membre de l’organisation propriétaire des deux entités pour pouvoir utiliser cette fonctionnalité.

## <a name="joining-the-meeting-chat"></a>Participation à la conversation d’une réunion 

Une fois l’interopérabilité de Teams activée, un utilisateur de Communication Services peut rejoindre l’appel Teams en tant qu’utilisateur externe à l’aide du kit de développement logiciel (SDK) Calling. En rejoignant l’appel, il est également ajouté comme participant à la conversation de la réunion, où il peut échanger des messages avec d’autres utilisateurs lors de l’appel. L’utilisateur n’aura pas accès aux messages de conversation qui ont été envoyés avant qu’il ne rejoigne l’appel. Pour participer à la réunion et commencer à converser, vous pouvez suivre les étapes suivantes.

## <a name="add-chat-to-the-teams-calling-app"></a>Ajouter la conversation à l’application d’appel Teams

Supprimez `Podfile.lock` et remplacez le contenu du Podfile par le code suivant :

```
platform :ios, '13.0'
use_frameworks!

target 'AzureCommunicationCallingSample' do
  pod 'AzureCommunicationCalling', '~> 1.0.0-beta.12'
  pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
end
```

## <a name="install-the-chat-packages"></a>Installer les packages de chat

Exécutez `pod install` pour installer le package AzureCommunicationChat.
Après l’installation, ouvrez le fichier `.xcworkspace`.

## <a name="set-up-the-app-framework"></a>Configurer le framework d’application

Importez le package AzureCommunicationChat dans `ContentView.swift` en ajoutant l’extrait de code suivant :

```
import AzureCommunicationChat
```

## <a name="add-the-teams-ui-controls"></a>Ajouter les contrôles d’interface utilisateur de Teams

Dans `ContentView.swift`, ajoutez l’extrait de code suivant sous les variables d’état existantes. 

```
    // Chat
    @State var chatClient: ChatClient?
    @State var chatThreadClient: ChatThreadClient?
    @State var chatMessage: String = ""
    @State var meetingMessages: [MeetingMessage] = []

    let displayName: String = "<YOUR_DISPLAY_NAME_HERE>"
```

Remplacez `<YOUR_DISPLAY_NAME_HERE>` par le nom complet à utiliser dans la conversation.

Ensuite, nous allons modifier le formulaire `Section` pour afficher nos messages de conversation et ajouter des contrôles d’interface utilisateur pour la conversation.

Ajoutez l’extrait de code suivant au formulaire existant, juste après l’affichage de texte `Text(recordingStatus)`, pour l’état de l’enregistrement.

```
VStack(alignment: .leading) {
    ForEach(meetingMessages, id: \.id) { message in
        let currentUser: Bool = (message.displayName == self.displayName)
        let foregroundColor = currentUser ? Color.white : Color.black
        let background = currentUser ? Color.blue : Color(.systemGray6)
        let alignment = currentUser ? HorizontalAlignment.trailing : HorizontalAlignment.leading
        VStack {
            Text(message.displayName).font(Font.system(size: 10))
            Text(message.content)
        }
        .alignmentGuide(.leading) { d in d[alignment] }
        .padding(10)
        .foregroundColor(foregroundColor)
        .background(background)
        .cornerRadius(10)
    }
}.frame(minWidth: 0, maxWidth: .infinity)
TextField("Enter your message...", text: $chatMessage)
Button(action: sendMessage) {
    Text("Send Message")
}.disabled(chatThreadClient == nil)
```

Ensuite, remplacez le titre par `Chat Teams Quickstart`. Modifiez la ligne suivante avec ce titre.

```
.navigationBarTitle("Chat Teams Quickstart")
```

## <a name="enable-the-teams-ui-controls"></a>Activer les contrôles d’interface utilisateur de Teams

### <a name="initialize-the-chatclient"></a>Initialiser le ChatClient

Instanciez le `ChatClient` et activez les notifications en temps réel. Nous allons utiliser des notifications en temps réel pour la réception des messages de conversation.

Dans `NavigationView` `.onAppear`, ajoutez l’extrait de code ci-dessous, après le code existant qui initialise le `CallAgent`.

```
// Initialize the ChatClient
do {
    let endpoint = "COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>"
    let credential = try CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN_HERE>")

    self.chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: AzureCommunicationChatClientOptions()
    )

    self.message = "ChatClient successfully created"

    // Start real-time notifications
    self.chatClient?.startRealTimeNotifications() { result in
        switch result {
        case .success:
            print("Real-time notifications started")
            // Receive chat messages
            self.chatClient?.register(event: ChatEventId.chatMessageReceived, handler: receiveMessage)
        case .failure:
            print("Failed to start real-time notifications")
            self.message = "Failed to enable chat notifications"
        }
    }
} catch {
    print("Unable to create ChatClient")
    self.message = "Please enter a valid endpoint and Chat token in source code"
    return
}
```

Remplacez `<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT_HERE>` par le point de terminaison de votre ressource Communication Services.
Remplacez `<USER_ACCESS_TOKEN_HERE>` par un jeton d’accès qui a l’étendue de conversation. 

En découvrir plus sur les jetons d’accès utilisateur : [Jeton d’accès utilisateur](../../access-tokens.md)

### <a name="initialize-the-chatthreadclient"></a>Initialiser le ChatThreadClient

Dans la fonction `joinTeamsMeeting()` existante, nous allons initialiser le `ChatThreadClient` après que l’utilisateur a rejoint la réunion.

Dans le gestionnaire d’achèvement de l’appel à `self.callAgent?.join()`, ajouter le code sous le commentaire `// Initialize the ChatThreadClient`. Le code complet est indiqué ci-dessous.

```
self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) { (call, error) in
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.message = "Teams meeting joined successfully"

        // Initialize the ChatThreadClient
        do {
            guard let threadId = getThreadId(from: self.meetingLink) else {
                self.message = "Failed to join meeting chat"
                return
            }
            self.chatThreadClient = try chatClient?.createClient(forThread: threadId)
            self.message = "Joined meeting chat successfully"
        } catch {
            print("Failed to create ChatThreadClient")
            self.message = "Failed to join meeting chat"
            return
        }
    } else {
        print("Failed to join Teams meeting")
    }
}
```

Ajoutez la fonction d’assistance suivante au `ContentView`, qui est utilisée pour récupérer l’ID de thread de conversation à partir du lien de réunion de l’équipe.

```
func getThreadId(from meetingLink: String) -> String? {
    if let range = self.meetingLink.range(of: "meetup-join/") {
        let thread = self.meetingLink[range.upperBound...]
        if let endRange = thread.range(of: "/")?.lowerBound {
            return String(thread.prefix(upTo: endRange))
        }
    }
    return nil
}
```

### <a name="enable-sending-messages"></a>Activer l’envoi des messages

Ajoutez la fonction `sendMessage()` à `ContentView`. Cette fonction utilise `ChatThreadClient` pour envoyer des messages à partir de l’utilisateur.

```
func sendMessage() {
    let message = SendChatMessageRequest(
        content: self.chatMessage,
        senderDisplayName: self.displayName
    )
    
    self.chatThreadClient?.send(message: message) { result, _ in
        switch result {
        case .success:
            print("Chat message sent")
        case .failure:
            print("Failed to send chat message")
        }

        self.chatMessage = ""
    }
}
```

### <a name="enable-receiving-messages"></a>Activer la réception des messages

Pour recevoir des messages, nous allons implémenter le gestionnaire des événements `ChatMessageReceived`. Quand de nouveaux messages sont envoyés au thread, ce gestionnaire ajoute les messages à la variable `meetingMessages` afin qu’ils puissent être affichés dans l’interface utilisateur.

Tout d’abord, ajoutez le struct suivant à `ContentView.swift`. L’interface utilisateur utilise les données du struct pour afficher les messages de conversation.

```
struct MeetingMessage {
    let id: String
    let content: String
    let displayName: String
}
```

Ensuite, ajoutez la fonction `receiveMessage()` à `ContentView`. Il s’agit du gestionnaire qui est appelé chaque fois qu’un événement `ChatMessageReceived` se produit.

```
func receiveMessage(response: Any, eventId: ChatEventId) {
    let chatEvent: ChatMessageReceivedEvent = response as! ChatMessageReceivedEvent

    let displayName: String = chatEvent.senderDisplayName ?? "Unknown User"
    let content: String = chatEvent.message.replacingOccurrences(of: "<[^>]+>", with: "", options: String.CompareOptions.regularExpression)

    self.meetingMessages.append(
        MeetingMessage(
            id: chatEvent.id,
            content: content,
            displayName: displayName
        )
    )
}
```

### <a name="leave-the-chat"></a>Quitter la conversation

Quand l’utilisateur quitte la réunion de l’équipe, nous effaçons la conversation de l’interface utilisateur. Le code complet est indiqué ci-dessous.

```
func leaveMeeting() {
    if let call = call {
        call.hangUp(options: nil, completionHandler: { (error) in
            if error == nil {
                self.message = "Leaving Teams meeting was successful"
                // Clear the chat
                self.meetingMessages.removeAll()
            } else {
                self.message = "Leaving Teams meeting failed"
            }
        })
    } else {
        self.message = "No active call to hanup"
    }
}
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obtenir le fil de conversation d’une réunion Teams pour un utilisateur Communication Services

Il est possible récupérer le lien et la conversation de la réunion Teams à l’aide d’API Graph détaillées dans la [Documentation Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true). Le kit SDK Communication Services Calling accepte un lien de réunion Teams complet. Ce lien est renvoyé avec la ressource `onlineMeeting`, accessible sous la propriété [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Avec les [API Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true), vous pouvez également obtenir le `threadId`. La réponse aura un objet `chatInfo` contenant le `threadID`. 

Vous pouvez également récupérer les informations de réunion et l’ID de thread requis à partir de l’URL **Rejoindre la réunion** disponible dans l’invitation à la réunion Teams proprement dite.
Un lien de réunion Teams ressemble à ceci : `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Le `threadId` sera là où `meeting_chat_thread_id` se trouve dans le lien. Assurez-vous que le `meeting_chat_thread_id` n’est pas échappé avant de l’utiliser. Il doit être au format suivant : `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Exécuter le code

Exécutez l'application. 

Pour rejoindre la réunion Teams, entrez le lien de réunion de votre équipe dans l’interface utilisateur.

Une fois que vous avez rejoint la réunion de l’équipe, vous devez autoriser l’utilisateur à participer à la réunion dans le client de votre équipe. Une fois que l’utilisateur est admis et a rejoint la conversation, vous pouvez envoyer et recevoir des messages.

:::image type="content" source="../join-teams-meeting-chat-quickstart-ios.png" alt-text="Capture d’écran de l’application iOS terminée.":::

> [!NOTE] 
> Actuellement, seuls l’envoi, la réception et la modification de messages sont pris en charge pour les scénarios d’interopérabilité avec Teams. D’autres fonctionnalités, telles que les indicateurs de saisie et les utilisateurs Communication Services qui ajoutent ou suppriment d’autres utilisateurs dans la réunion Teams, ne sont pas prises en charge pour l’instant.