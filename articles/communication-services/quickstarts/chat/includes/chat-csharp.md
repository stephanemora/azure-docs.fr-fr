---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 4c8bd66dde54ff90ea2191fba58f10c87c45cf68
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958093"
---
## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous de :
- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer une ressource Azure Communication](../../create-communication-resource.md). Vous devrez inscrire le **point de terminaison** de votre ressource pour ce démarrage rapide.
- [Jeton d’accès utilisateur](../../access-tokens.md). Veillez à définir l’étendue sur « chat » (conversation) et notez la chaîne token ainsi que la chaîne userId.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-c-application"></a>Créer une application C#

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `ChatQuickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Remplacez votre répertoire par le dossier d’application que vous venez de créer, puis utilisez la commande `dotnet build` pour compiler votre application.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Installer le package

Installer le kit de développement logiciel (SDK) Azure Communication Chat pour .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0
```

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent quelques-unes des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services Chat pour C#.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat). Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des fils de conversation. |
| ChatThreadClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread). Vous obtenez une instance via ChatClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des participants, envoyer des notifications de saisie et des accusés de lecture. |

## <a name="create-a-chat-client"></a>Créer un client de conversation

Pour créer un client de conversation, vous allez utiliser votre point de terminaison Communication Services ainsi que le jeton d’accès qui a été généré au cours des étapes prérequises. Vous devez utiliser la classe `CommunicationIdentityClient` du kit de développement logiciel (SDK) Identity pour créer un utilisateur et émettre un jeton à transmettre à votre client de conversation.

Apprenez-en davantage sur les [jetons d’accès utilisateur](../../access-tokens.md).

Ce guide de démarrage rapide ne couvre pas la création d’un niveau de service pour gérer les jetons de votre application de conversation, bien que cette opération soit recommandée. En savoir plus sur l’[Architecture de conversation](../../../concepts/chat/concepts.md)

Copiez les extraits de code suivants et collez-les dans le fichier source : **Program.cs**
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

Utilisez la méthode `createChatThread` sur chatClient pour créer un fil de conversation
- Utilisez `topic` pour attribuer un sujet à cette conversation ; le sujet peut être mis à jour après que le fil de conversation a été créé à l’aide de la fonction `UpdateTopic`.
- Utilisez la propriété `participants` pour transmettre la liste d’objets `ChatParticipant` à ajouter au fil de conversation. L’objet `ChatParticipant` est initialisé avec un objet `CommunicationIdentifier`. `CommunicationIdentifier` peut être de type `CommunicationUserIdentifier`, `MicrosoftTeamsUserIdentifier` ou `PhoneNumberIdentifier`. Par exemple, pour obtenir un objet `CommunicationIdentifier`, vous devez transmettre un ID d’accès que vous avez créé en suivant les instructions fournies dans [Créer un utilisateur](../../access-tokens.md#create-an-identity)

L’objet de réponse de la méthode `createChatThread` contient les détails de `chatThread`. Pour interagir avec les opérations du fil de conversation, telles que l’ajout de participants, l’envoi d’un message, la suppression d’un message, etc., une instance de client `chatThreadClient` doit être instanciée à l’aide de la méthode `GetChatThreadClient` sur le client `ChatClient`.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation
La méthode `GetChatThreadClient` retourne un client de fil pour un fil qui existe déjà. Il peut être utilisé dans les opérations effectuées sur le fil créé : ajout de membres, envoi d’un message, etc. threadId est l’ID unique du fil de conversation existant.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>Répertorier tous les fils de conversation
Utilisez `GetChatThreads` pour récupérer tous les fils de conversation dont fait partie l’utilisateur.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Utilisez `SendMessage` pour envoyer un message à un fil de conversation.

- Utilisez `content` pour fournir le contenu du message (obligatoire).
- Utilisez `type` pour le type de contenu du message tel que « Text » ou « Html ». S’il n’est pas spécifié, « Text » est défini.
- Utilisez `senderDisplayName` pour spécifier le nom d’affichage de l’expéditeur. S’il n’est pas spécifié, une chaîne vide est définie.

```csharp
SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recevoir les messages de conversation d’un fil de conversation

Vous pouvez récupérer les messages de conversation en interrogeant la méthode `GetMessages` sur le client de fil de conversation selon des intervalles définis.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` accepte un paramètre `DateTimeOffset` facultatif. Si ce décalage est spécifié, vous recevez les messages qui ont été reçus, mis à jour ou supprimés après cette période. Notez que les messages reçus avant la période de décalage, mais qui ont été modifiés ou supprimés après ladite période sont également retournés.

`GetMessages` retourne la version la plus récente du message, avec les modifications ou les suppressions dont le message a éventuellement fait l’objet via `UpdateMessage` et `DeleteMessage`. Pour les messages supprimés, `chatMessage.DeletedOn` retourne une valeur datetime indiquant à quel moment ce message a été supprimé. Pour les messages modifiés, `chatMessage.EditedOn` retourne une valeur datetime indiquant à quel moment ce message a été modifié. Il est possible d’accéder à l’heure initiale de création du message à l’aide de `chatMessage.CreatedOn` ; elle peut être utilisée à des fins de classement des messages.

`GetMessages` retourne différents types de messages qui peuvent être identifiés par `chatMessage.Type`. Ces types sont les suivants :

- `Text`: Message de conversation ordinaire envoyé par un membre du fil.

- `Html` : message texte mis en forme. Notez que les utilisateurs Communication Services ne peuvent actuellement pas envoyer de messages en texte enrichi. Ce type de message est pris en charge par les messages qu’envoient les utilisateurs Teams aux utilisateurs Communication Services dans les scénarios d’interopérabilité de Teams.

- `TopicUpdated`: Message système qui indique que le sujet a été mis à jour. (lecture seule)

- `ParticipantAdded` : message système qui indique qu’un ou plusieurs participants ont été ajoutés au fil de conversation. (lecture seule)

- `ParticipantRemoved` : Message système qui indique qu’un participant a été supprimé du fil de conversation.

Pour plus d’informations, consultez [Types de messages](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Une fois qu’un fil est créé, vous pouvez y ajouter des utilisateurs ou en supprimer. En ajoutant des utilisateurs, vous leur permettez d’envoyer des messages au fil de conversation et d’ajouter ou de supprimer d’autres participants. Avant d’appeler `AddParticipants`, vérifiez que vous avez acquis un nouveau jeton d’accès et une identité pour cet utilisateur. L’utilisateur aura besoin de ce jeton d’accès pour initialiser son client de conversation.

Utilisez `AddParticipants` pour ajouter un ou plusieurs participants au fil de conversation. Voici les attributs pris en charge pour chaque participant au fil de conversation :
- `communicationUser`, obligatoire. Il s’agit de l’identité du participant au fil de conversation.
- `displayName`, facultatif, est le nom d’affichage pour le participant au fil.
- `shareHistoryTime`, facultatif. Il s’agit du moment à partir duquel l’historique de conversation est partagé avec le participant.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>Obtenir les participants au fil de conversation

Utilisez `GetParticipants` pour récupérer les participants au fil de conversation.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>Envoyer une confirmation de lecture

Utilisez `SendReadReceipt` pour informer les autres participants que le message est lu par l’utilisateur.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```console
dotnet run
```
