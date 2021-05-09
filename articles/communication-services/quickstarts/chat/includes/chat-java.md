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
ms.openlocfilehash: 21e3650b71eea858f776bc6c96a6c0bff9c10154
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313601"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) version 8 ou ultérieure.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Chaîne de connexion et ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md).
- [Jeton d’accès utilisateur](../../access-tokens.md). Veillez à définir l’étendue sur « chat » (conversation) et notez la chaîne token ainsi que la chaîne userId.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-java-application"></a>Créer une application Java

Ouvrez votre terminal ou votre fenêtre Commande, puis accédez au répertoire dans lequel vous souhaitez créer votre application Java. Exécutez la commande ci-dessous pour générer le projet Java à partir du modèle maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Vous remarquerez que l’objectif « generate » a créé un répertoire portant le même nom que l’artifactId. Sous ce répertoire, `src/main/java directory` contient le code source du projet, le répertoire `src/test/java` contient la source de test et le fichier pom.xml est le modèle objet du projet, ou [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Mettez à jour le fichier POM de votre application pour utiliser Java 8 ou version ultérieure :

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Ajouter les références de package pour le kit de développement logiciel (SDK) Chat

Dans votre fichier POM, référencez le package `azure-communication-chat` avec les API de conversation (Chat) :

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

Pour l’authentification, votre client doit référencer le package `azure-communication-common` :

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent quelques-unes des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services Chat pour Java.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat). Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des fils de conversation. |
| ChatAsyncClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat) asynchrone. Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des fils de conversation. |
| ChatThreadClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread). Vous obtenez une instance via ChatClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des utilisateurs, envoyer des notifications de saisie et des accusés de lecture. |
| ChatThreadAsyncClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread) asynchrone. Vous obtenez une instance via ChatAsyncClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des utilisateurs, envoyer des notifications de saisie et des accusés de lecture. |

## <a name="create-a-chat-client"></a>Créer un client de conversation
Pour créer un client de conversation, vous allez utiliser le point de terminaison Communication Services ainsi que le jeton d’accès qui a été généré au cours des étapes prérequises. Les jetons d’accès utilisateur vous permettent de créer des applications clientes qui s’authentifient directement auprès d’Azure Communication Services. Une fois que vous avez généré ces jetons sur votre serveur, transmettez-les en retour à un appareil client. Vous devez utiliser la classe CommunicationTokenCredential du kit de développement logiciel (SDK) commun pour transmettre le jeton à votre client de conversation.

En savoir plus sur l’[Architecture de conversation](../../../concepts/chat/concepts.md)

Si vous devez ajouter des instructions d’importation, veillez à ajouter uniquement des importations des espaces de noms com.azure.communication.chat et com.azure.communication.chat.models, et non de l’espace de noms com.azure.communication.chat.implementation. Dans le fichier App.java qui a été généré via Maven, vous pouvez commencer avec le code suivant :

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

Utilisez la méthode `createChatThread` pour créer un fil de conversation.
`createChatThreadOptions` est utilisé pour décrire la demande de fil.

- Utilisez le paramètre `topic` du constructeur pour attribuer un sujet à cette conversation ; le sujet peut être mis à jour après que le fil de conversation a été créé à l’aide de la fonction `UpdateThread`.
- Utilisez `participants` pour lister les participants au fil à ajouter à la conversation. `ChatParticipant` prend l’utilisateur que vous avez créé dans le démarrage rapide [Jeton d’accès utilisateur](../../access-tokens.md).

`CreateChatThreadResult` correspond à la réponse renvoyée par la création d’un fil de conversation.
Elle contient une méthode `getChatThread()` qui renvoie l'objet `ChatThread` permettant d’obtenir le client de fil de conversation à partir duquel obtenir `ChatThreadClient` pour effectuer des opérations sur la conversation créée : ajout de participants, envoi d’un message, etc. L'objet `ChatThread` contient également la méthode `getId()` qui récupère l’ID unique de la conversation.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Répertorier les fils de conversation

Utilisez la méthode `listChatThreads` pour récupérer la liste des fils de conversation existants.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation

La méthode `getChatThreadClient` retourne un client de fil pour un fil qui existe déjà. Elle peut être utilisée pour effectuer des opérations sur le fil créé : ajout de participants, envoi d’un message, etc. `chatThreadId` est l’ID unique du fil de conversation existant.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Utilisez la méthode `sendMessage` pour envoyer un message au fil que vous venez de créer, identifié par chatThreadId.
`sendChatMessageOptions` est utilisé pour décrire la demande de message de conversation.

- Utilisez `content` pour fournir le contenu du message de conversation.
- Utilisez `type` pour spécifier le type de contenu du message de conversation, TEXTE ou HTML.
- Utilisez `senderDisplayName` pour spécifier le nom d’affichage de l’expéditeur.

La réponse `sendChatMessageResult` contient un `id`, qui est l’ID unique du message.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recevoir les messages de conversation d’un fil de conversation

Vous pouvez récupérer les messages de conversation en interrogeant la méthode `listMessages` sur le client de fil de conversation selon des intervalles définis.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` retourne la version la plus récente du message, avec les modifications ou les suppressions dont le message a éventuellement fait l’objet via .editMessage() et .deleteMessage(). Pour les messages supprimés, `chatMessage.getDeletedOn()` retourne une valeur datetime indiquant à quel moment ce message a été supprimé. Pour les messages modifiés, `chatMessage.getEditedOn()` retourne une valeur datetime indiquant à quel moment ce message a été modifié. Il est possible d’accéder à l’heure initiale de création du message à l’aide de `chatMessage.getCreatedOn()` ; elle peut être utilisée à des fins de classement des messages.

Pour en savoir plus sur les types de messages, consultez [Types de messages](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Envoyer une confirmation de lecture

Utilisez la méthode `sendReadReceipt` pour publier un événement de confirmation de lecture sur un fil de conversation, pour le compte d’un utilisateur.
`chatMessageId` correspond à ID unique du message de conversation lu.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Répertorier les participants à la conversation

Utilisez `listParticipants` pour récupérer une collection paginée contenant les participants du fil de conversation identifié par chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Une fois qu’un fil de conversation est créé, vous pouvez y ajouter des utilisateurs et en supprimer. En ajoutant des utilisateurs, vous leur permettez d’envoyer des messages au fil de conversation et d’ajouter/supprimer d’autres participants. Vous devez commencer par obtenir un nouveau jeton d’accès et une identité pour cet utilisateur. Avant d’appeler la méthode addParticipants, vérifiez que vous avez acquis un nouveau jeton d’accès et une identité pour cet utilisateur. L’utilisateur aura besoin de ce jeton d’accès pour initialiser son client de conversation.

Utilisez la méthode `addParticipants` pour ajouter des participants au fil.

- `communicationIdentifier`, obligatoire. Il s’agit du CommunicationIdentifier que vous avez créé avec le CommunicationIdentityClient dans le guide de démarrage rapide [Jeton d’accès utilisateur](../../access-tokens.md).
- `displayName`, facultatif, est le nom d’affichage pour le participant au fil.
- `shareHistoryTime`, facultatif, est le moment à partir duquel l’historique de conversation est partagé avec le participant. Pour partager l’historique depuis le début du fil de conversation, attribuez à cette propriété une date égale ou antérieure à la date de création du fil. Pour ne pas partager l’historique antérieur au moment où le participant a été ajouté, définissez-la sur l’heure actuelle. Pour partager un historique partiel, attribuez-lui la date de votre choix.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Exécuter le code

Accédez au répertoire contenant le fichier *pom.xml*, puis compilez le projet à l’aide de la commande `mvn` suivante.

```console
mvn compile
```

Ensuite, générez le package.

```console
mvn package
```

Exécutez la commande `mvn` suivante pour exécuter l’application.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```