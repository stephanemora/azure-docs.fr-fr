---
title: Fichier Include
description: inclure fichier
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1676ed849c025fc0f41aac933268e80276feeee2
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114723695"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="sample-code"></a>Exemple de code
Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/add-chat).

## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Installez [Xcode](https://developer.apple.com/xcode/) et [CocoaPods](https://cocoapods.org/). Vous utilisez Xcode pour créer une application iOS servant au guide de démarrage rapide, et CocoaPods pour installer les dépendances.
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Démarrage rapide : Créer et gérer des ressources Communication Services](../../create-communication-resource.md). Pour ce guide de démarrage rapide, vous devez enregistrer le point de terminaison de votre ressource.
- Créez deux utilisateurs dans Azure Communication Services, et émettez pour eux un [jeton d’accès utilisateur](../../access-tokens.md). Veillez à définir l’étendue sur `chat`, puis prenez note de la chaîne `token` et de la chaîne `userId`. Dans ce guide de démarrage rapide, vous créez un fil de conversation avec un participant initial, puis vous y ajoutez un deuxième participant.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-ios-application"></a>Créer une application iOS

Ouvrez Xcode et sélectionnez **Create a new Xcode project** (Créer un projet Xcode). Sélectionnez ensuite **iOS** comme plateforme, et **Application** pour le modèle.

En guise de nom de projet, entrez **ChatQuickstart**. Sélectionnez ensuite **Storyboard** comme interface, **UIKit App Delegate** comme cycle de vie et **SWIFT** comme langage.

Sélectionnez **Suivant**, puis choisissez le répertoire où vous voulez créer le projet.

### <a name="install-the-libraries"></a>Installer les bibliothèques

Utilisez CocoaPods pour installer les dépendances nécessaires de Communication Services.

À partir de la ligne de commande, accédez au répertoire racine du projet iOS `ChatQuickstart`. Créez un Podfile avec le contenu suivant : `pod init`.

Ouvrez le Podfile et ajoutez les dépendances suivantes à la cible `ChatQuickstart` :

```
pod 'AzureCommunicationCommon', '~> 1.0'
pod 'AzureCommunicationChat', '~> 1.0.1'
```

Installez les dépendances à l’aide de la commande suivante : `pod install`. Notez que cette opération crée également un espace de travail Xcode.

Après l’exécution de `pod install`, rouvrez le projet dans Xcode en sélectionnant le nouveau `.xcworkspace`.

### <a name="set-up-the-placeholders"></a>Configurer les espaces réservés

Ouvrez l’espace de travail `ChatQuickstart.xcworkspace` dans Xcode, puis ouvrez `ViewController.swift`.

Dans ce guide de démarrage rapide, vous ajoutez votre code à `viewController` et affichez la sortie dans la console Xcode. Ce guide de démarrage rapide ne traite pas de la création d’une interface utilisateur dans iOS. 

En haut de `viewController.swift`, importez les bibliothèques `AzureCommunication` et `AzureCommunicatonChat` :

```
import AzureCommunicationCommon
import AzureCommunicationChat
```

Copiez le code suivant dans la méthode `viewDidLoad()` de `ViewController` :

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

À des fins de démonstration, nous allons utiliser un sémaphore pour synchroniser votre code. Dans les étapes suivantes, vous remplacez les espaces réservés par un exemple de code en utilisant la bibliothèque Conversation Azure Communication Services.


### <a name="create-a-chat-client"></a>Créer un client de conversation

Remplacez le commentaire `<CREATE A CHAT CLIENT>` par le code suivant :

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

Remplacez `<ACS_RESOURCE_ENDPOINT>` par le point de terminaison de votre ressource Azure Communication Services. Remplacez `<ACCESS_TOKEN>` par un jeton d’accès Communication Services valide.

Ce guide de démarrage rapide ne couvre pas la création d’un niveau de service permettant de gérer les jetons de votre application de conversation, mais cette opération est recommandée. Pour plus d’informations, consultez la section « Architecture des conversations » de [Concepts relatifs aux conversations](../../../concepts/chat/concepts.md).

Pour obtenir plus d’informations sur les jetons d’accès utilisateur, consultez [Démarrage rapide : Créer et gérer des jetons d’accès](../../access-tokens.md).

## <a name="object-model"></a>Modèle objet 

Les classes et interfaces suivantes gèrent quelques-unes des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services Chat pour JavaScript.

| Nom                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Cette classe est nécessaire à la fonctionnalité de conversation. Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des threads et pour vous abonner à des événements de conversation. |
| `ChatThreadClient` | Cette classe est nécessaire à la fonctionnalité de fil de conversation. Vous obtenez une instance par le biais de `ChatClient` et vous l’utilisez pour envoyer, recevoir, mettre à jour et supprimer des messages. Vous pouvez également l’utiliser pour ajouter, supprimer et obtenir des utilisateurs, ainsi qu’envoyer des notifications de saisie et des confirmations de lecture. |

## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

À présent, utilisez votre `ChatClient` pour créer un fil avec un utilisateur initial.

Remplacez le commentaire `<CREATE A CHAT THREAD>` par le code suivant :

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Remplacez `<USER_ID>` par un ID d’utilisateur Communication Services valide.

Vous utilisez ici un sémaphore pour attendre le gestionnaire d’achèvement avant de continuer. Dans les étapes ultérieures, vous utiliserez le `threadId` de la réponse retournée au gestionnaire d’achèvement.

## <a name="list-all-chat-threads"></a>Répertorier tous les fils de conversation

Après la création d’un fil de conversation, nous pouvons lister tous les fils de conversation en appelant la méthode `listChatThreads` sur `ChatClient`. Remplacez le commentaire `<LIST ALL CHAT THREADS>` par le code suivant :

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(threads):
        guard let chatThreadItems = threads.pageItems else {
            print("No threads returned.")
            return
        }

        for chatThreadItem in chatThreadItems {
            print("Thread id: \(chatThreadItem.id)")
        }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation

Maintenant que vous avez créé un fil de conversation, vous pouvez obtenir un `ChatThreadClient` pour effectuer des opérations au sein du fil.

Remplacez le commentaire `<GET A CHAT THREAD CLIENT>` par le code suivant :

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Remplacez le commentaire `<SEND A MESSAGE>` par le code suivant :

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Tout d’abord, vous élaborez l’élément `SendChatMessageRequest` qui renferme le contenu et le nom d’affichage de l’expéditeur. Cette requête peut également contenir l’heure du partage de l’historique, si vous souhaitez l’inclure. La réponse retournée au gestionnaire d’achèvement contient l’ID du message qui a été envoyé.


## <a name="send-a-read-receipt"></a>Envoyer une confirmation de lecture

Vous pouvez envoyer une confirmation de lecture pour un message particulier en appelant la méthode `ChatThreadClients` `sendReadReceipt`. Remplacez le commentaire `<SEND A READ RECEIPT>` par le code suivant :

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recevoir les messages de conversation d’un fil de conversation

Vous pouvez recevoir des messages d’un fil de conversation en appelant la méthode `listMessages()` à partir de `ChatThreadClient`. Les messages de liste incluent les messages système ainsi que les messages envoyés par l’utilisateur. Pour plus d’informations sur les types de messages que vous pouvez recevoir, consultez [Types de messages](../../../concepts/chat/concepts.md#message-types)

Remplacez le commentaire `<RECEIVE MESSAGES>` par le code suivant :

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messagesResult):
        guard let messages = messagesResult.pageItems else {
            print("No messages returned.")
            return
        }

        for message in messages {
            print("Received message with id: \(message.id)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Remplacez le commentaire `<ADD A USER>` par le code suivant :

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

Remplacez `<USER_ID>` par l’ID d’utilisateur Communication Services de l’utilisateur à ajouter.

Lorsque vous ajoutez un participant à un fil, la réponse retournée peut contenir des erreurs. Ces erreurs représentent l’échec de l’ajout de participants particuliers.

## <a name="list-users-in-a-thread"></a>Lister les utilisateurs dans un fil de conversation

Remplacez le commentaire `<LIST USERS>` par le code suivant :

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participantsResult):
        guard let participants = participantsResult.pageItems else {
            print("No participants returned.")
            return
        }

        for participant in participants {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>Exécuter le code

Dans Xcode, cliquez sur le bouton Run (Exécuter) pour générer et exécuter le projet. Dans la console, vous pouvez voir la sortie du code et la sortie de l’enregistreur d’événements du ChatClient.

**Remarque :** Définissez `Build Settings > Build Options > Enable Bitcode` sur `No`. Actuellement, le Kit de développement logiciel (SDK) AzureCommunicationChat pour iOS ne prend pas en charge l’activation du Bitcode. Le [problème suivant](https://github.com/Azure/azure-sdk-for-ios/issues/787) est suivi sur GitHub.
