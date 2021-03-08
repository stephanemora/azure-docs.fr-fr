---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/11/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 5c79ea68e648cd3d78f94eb2272b6f32e3c4806f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750924"
---
## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installez [Xcode](https://developer.apple.com/xcode/) et [Cocoapods](https://cocoapods.org/) : nous allons utiliser Xcode pour créer une application iOS pour le guide de démarrage rapide et Cocoapods pour installer les dépendances.
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../create-communication-resource.md). Vous devrez **enregistrer le point de terminaison de votre ressource** pour ce guide de démarrage rapide.
- Créez **deux** utilisateurs ACS et émettez pour eux un jeton d’accès utilisateur [Jeton d’accès utilisateur](../../access-tokens.md). Veillez à définir l’étendue sur **chat** (conversation) et **prenez note de la chaîne du jeton et de la chaîne userId**. Dans ce guide de démarrage rapide, nous allons créer un fil de conversation avec un participant initial, puis ajouter un deuxième participant au fil.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-ios-application"></a>Créer une application iOS

Ouvrez Xcode et sélectionnez `Create a new Xcode project` (Créer un projet Xcode).

Dans la fenêtre suivante, sélectionnez `iOS` comme plateforme et `App` pour le modèle.

Quand choisissez les options, entrez `ChatQuickstart` comme nom du projet. Sélectionnez `Storyboard` comme interface, `UIKit App Delegate` comme cycle de vie et `Swift` comme langage.

Cliquez sur Suivant, puis choisissez le répertoire où vous voulez créer le projet.

### <a name="install-the-libraries"></a>Installer les bibliothèques

Nous allons utiliser Cocoapods pour installer les dépendances nécessaires de Communication Services.

À partir de la ligne de commande, accédez au répertoire racine du projet iOS `ChatQuickstart`.

Créez un Podfile : `pod init`

Ouvrez le Podfile et ajoutez les dépendances suivantes à la cible `ChatQuickstart` :
```
pod 'AzureCommunication', '~> 1.0.0-beta.8'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.8'
```

Installez les dépendances ; cette opération crée également un espace de travail Xcode : `pod install`

**Après l’exécution de l’installation du pod, rouvrez le projet dans Xcode en sélectionnant le nouveau `.xcworkspace`.**

### <a name="setup-the-placeholders"></a>Configurer les espaces réservés

Ouvrez l’espace de travail `ChatQuickstart.xcworkspace` dans Xcode, puis ouvrez `ViewController.swift`.

Dans ce guide de démarrage rapide, nous allons ajouter notre code à `viewController` et afficher la sortie dans la console Xcode. Ce guide de démarrage rapide ne traite pas de la création d’une interface utilisateur dans iOS. 

En haut de `viewController.swift`, importez les bibliothèques `AzureCommunication` et `AzureCommunicatonChat` :

```
import AzureCommunication
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
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Nous allons utiliser un sémaphore pour synchroniser notre code à des fins de démonstration. Dans les étapes suivantes, nous allons remplacer les espaces réservés par un exemple de code en utilisant la bibliothèque Azure Communication Services Chat.


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

Remplacez `<ACS_RESOURCE_ENDPOINT>` par le point de terminaison de votre ressource ACS.
Remplacez `<ACCESS_TOKEN>` par un jeton d’accès ACS valide.

Ce guide de démarrage rapide ne couvre pas la création d’un niveau de service pour gérer les jetons de votre application de conversation, bien que cette opération soit recommandée. Consultez la documentation suivante pour plus d’informations sur l’[Architecture de conversation](../../../concepts/chat/concepts.md)

Apprenez-en davantage sur les [jetons d’accès utilisateur](../../access-tokens.md).

## <a name="object-model"></a>Modèle objet 
Les classes et interfaces suivantes gèrent quelques-unes des principales fonctionnalités de la bibliothèque de client Azure Communication Services Chat pour JavaScript.

| Nom                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat). Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des fils de conversation. |
| ChatThreadClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread). Vous obtenez une instance via ChatClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des utilisateurs, envoyer des notifications de saisie et des accusés de lecture, souscrire à des événements de conversation. |

## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

Nous allons maintenant utiliser notre `ChatClient` pour créer un fil de conversation avec un utilisateur initial.

Remplacez le commentaire `<CREATE A CHAT THREAD>` par le code suivant :

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: "<USER_ID>",
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Remplacez `<<USER_ID>>` par un ID d’utilisateur Communication Services valide.

Nous utilisons ici un sémaphore pour attendre le gestionnaire d’achèvement avant de continuer. Dans les étapes ultérieures, nous allons utiliser le `threadId` de la réponse retournée au gestionnaire d’achèvement.

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation

Maintenant que nous avons créé un fil de conversation, nous obtenons un `ChatThreadClient` pour effectuer des opérations dans le fil.

Remplacez le commentaire `<CREATE A CHAT THREAD CLIENT>` par le code suivant :

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

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Nous construisons d’abord le `SendChatMessageRequest` qui contient le contenu et le nom d’affichage des expéditeurs (il peut aussi contenir l’heure de l’historique du partage). La réponse retournée au gestionnaire d’achèvement contient l’ID du message qui a été envoyé.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Remplacez le commentaire `<ADD A USER>` par le code suivant :

```
let user = Participant(
    id: "<USER_ID>",
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Remplacez `<USER_ID>` par l’ID d’utilisateur ACS de l’utilisateur à ajouter.

Lors de l’ajout d’un participant à un fil, la réponse retournée peut contenir des erreurs. Ces erreurs représentent l’échec de l’ajout de participants particuliers.

## <a name="list-users-in-a-thread"></a>Lister les utilisateurs dans un fil de conversation

Remplacez le commentaire `<LIST USERS>` par le code suivant :

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            print(participant.user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Supprimer un utilisateur d’un fil de conversation

Remplacez le commentaire `<REMOVE A USER>` par le code suivant :

```
chatThreadClient
    .remove(
        participant: "<USER_ID>"
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Remplacez `<USER ID>` par l’ID d’utilisateur de Communication Services du participant à supprimer.

## <a name="run-the-code"></a>Exécuter le code

Dans Xcode, cliquez sur le bouton Run (Exécuter) pour générer et exécuter le projet. Dans la console, vous pouvez voir la sortie du code et la sortie de l’enregistreur d’événements du ChatClient.


