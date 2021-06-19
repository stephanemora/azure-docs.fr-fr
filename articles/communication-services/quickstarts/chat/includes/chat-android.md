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
ms.openlocfilehash: 04f97e121317635ceeda764da8c75834c972ace8
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112042033"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

> [!NOTE]
> Vous trouverez le code finalisé pour ce guide de démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add-chat).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez [Android Studio](https://developer.android.com/studio) que nous allons utiliser afin de créer une application Android pour le démarrage rapide des l’installation dépendances.
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer des ressources Azure Communication Services](../../create-communication-resource.md). Vous devrez **enregistrer le point de terminaison de votre ressource** pour ce guide de démarrage rapide.
- Créez **deux** utilisateurs Azure Communication Services et émettez pour eux un jeton d’accès utilisateur [Jeton d’accès utilisateur](../../access-tokens.md). Veillez à définir l’étendue sur **chat** (conversation) et **prenez note de la chaîne du jeton et de la chaîne userId**. Dans ce démarrage rapide, nous allons créer un thread avec un participant initial, puis y ajouter un deuxième participant.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-android-application"></a>Créer une application Android

1. Ouvrez Android Studio, puis sélectionnez `Create a new project`. 
2. Dans la fenêtre suivante, sélectionnez `Empty Activity` en tant que modèle de projet.
3. Lors du choix des options, entrez `ChatQuickstart` comme nom du projet.
4. Cliquez sur Suivant, puis choisissez le répertoire où vous voulez créer le projet.

### <a name="install-the-libraries"></a>Installer les bibliothèques

Nous allons utiliser Gradle pour installer les dépendances nécessaires de Communication Services. À partir de la ligne de commande, accédez au répertoire racine du projet `ChatQuickstart`. Ouvrez le fichier build.gradle de l’application et ajoutez les dépendances suivantes à la cible `ChatQuickstart` :

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
implementation 'com.azure.android:azure-core-http-okhttp:1.0.0-beta.5'
implementation 'org.slf4j:slf4j-log4j12:1.7.29'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Exclure les fichiers méta dans les options d’empaquetage dans build.gradle à la racine
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>(Alternative) Pour installer des bibliothèques via Maven
Pour importer la bibliothèque dans votre projet à l’aide du système de génération [Maven](https://maven.apache.org/), ajoutez-la à la section `dependencies` du fichier `pom.xml` de votre application, en spécifiant son ID d’artefact et la version que vous souhaitez utiliser :

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Configurer les espaces réservés

Ouvrez et modifiez le fichier `MainActivity.java`. Dans ce démarrage rapide, nous allons ajouter notre code à `MainActivity`, puis afficher la sortie dans la console. Ce démarrage rapide ne traite pas de la génération d’une interface utilisateur. En haut du fichier, importez les bibliothèques `Communication common` et `Communication chat` et les autres bibliothèques système :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.chat.models.*;
import com.azure.android.communication.common.*;

import android.os.Bundle;
import android.util.Log;
import android.widget.Toast;

import com.jakewharton.threetenabp.AndroidThreeTen;
import org.threeten.bp.OffsetDateTime;

import java.util.ArrayList;
import java.util.List;
```

Copiez le code suivant dans la classe `MainActivity` du fichier `MainActivity.java` :

```java
    private String endpoint = "https://<resource>.communication.azure.com";
    private String firstUserId = "<first_user_id>";
    private String secondUserId = "<second_user_id>";
    private String firstUserAccessToken = "<first_user_access_token>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "Chat Quickstart App";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            AndroidThreeTen.init(this);

            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>
            
            // <RECEIVE CHAT MESSAGES>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

1. Remplacez `<resource>` par votre ressource Communication Services.
2. Remplacez `<first_user_id>` et `<second_user_id>` par les ID d’utilisateur des services de communication valides qui ont été générés dans le cadre des étapes préalables.
3. Remplacez `<first_user_access_token>` par le jeton d’accès Communication Services de `<first_user_id>` qui a été généré dans le cadre des étapes préalables.

Dans les étapes suivantes, nous allons remplacer les espaces réservés par un exemple de code en utilisant la bibliothèque Azure Communication Services Chat.


### <a name="create-a-chat-client"></a>Créer un client de conversation

Remplacez le commentaire `<CREATE A CHAT CLIENT>` par le code suivant (placez les instructions d’importation en haut du fichier) :

```java
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(firstUserAccessToken, OffsetDateTime.now().plusDays(1))), "chat"))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

## <a name="object-model"></a>Modèle objet
Les classes et interfaces suivantes gèrent quelques-unes des principales fonctionnalités du kit de développement logiciel (SDK) Azure Communication Services Chat pour JavaScript.

| Nom                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat). Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des threads. |
| ChatThreadClient/ChatThreadAsyncClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread). Vous obtenez une instance via ChatClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des utilisateurs, envoyer des notifications de saisie et des accusés de lecture, souscrire à des événements de conversation. |

## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

Nous allons utiliser notre `ChatAsyncClient` pour créer un thread avec un utilisateur initial.

Remplacez le commentaire `<CREATE A CHAT THREAD>` par le code suivant :

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(firstUserId))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation

Maintenant que nous avons créé un thread de conversation, nous allons obtenir un `ChatThreadAsyncClient` pour effectuer des opérations dans le thread. Remplacez le commentaire `<CREATE A CHAT THREAD CLIENT>` par le code suivant :

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(firstUserAccessToken, OffsetDateTime.now().plusDays(1))), "chat"))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Nous allons envoyer le message à ce thread maintenant.

Remplacez le commentaire `<SEND A MESSAGE>` par le code suivant :

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recevoir les messages de conversation d’un fil de conversation
Avec la signalisation en temps réel, vous pouvez vous abonner aux nouveaux messages entrants et mettre à jour en conséquence les messages actuellement en mémoire. Azure Communication Services prend en charge une [liste d’événements auxquels vous pouvez vous abonner](../../../concepts/chat/concepts.md#real-time-notifications).

Mettez à jour le code du client de conversation pour ajouter `realtimeNotificationParams` :

```java
ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(firstUserAccessToken, OffsetDateTime.now().plusDays(1))), "chat"))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .realtimeNotificationParams(getApplicationContext(), firstUserAccessToken)
    .buildAsyncClient();

```

Remplacez le commentaire `<RECEIVE CHAT MESSAGES>` par le code suivant (placez les instructions d’importation en haut du fichier) :

```java
import com.azure.android.communication.chat.signaling.chatevents.BaseEvent;
import com.azure.android.communication.chat.signaling.chatevents.ChatMessageReceivedEvent;
import com.azure.android.communication.chat.signaling.properties.ChatEventId;

// Start real time notification
chatAsyncClient.startRealtimeNotifications();

// Register a listener for chatMessageReceived event
chatAsyncClient.on(ChatEventId.chatMessageReceived, "chatMessageReceived", (BaseEvent payload) -> {
    ChatMessageReceivedEvent chatMessageReceivedEvent = (ChatMessageReceivedEvent) payload;
    // You code to handle chatMessageReceived event
    
});

```

> [!IMPORTANT]
> Problème connu : lors de l’utilisation conjointe du chat Android et de l’appel du kit de développement logiciel (SDK) dans la même application, la fonctionnalité de notifications en temps réel du SDK du chat ne fonctionne pas. Vous risquez d’obtenir un problème de résolution de dépendance.
> Pendant que nous travaillons à une solution, vous pouvez désactiver la fonctionnalité de notifications en temps réel en ajoutant les informations de dépendance suivantes dans le fichier build.gradle de l’application et en interrogeant à la place l’API GetMessages pour présenter les messages entrants aux utilisateurs. 
> 
> ```
> implementation ("com.azure.android:azure-communication-chat:1.0.0-beta.8") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.9'
> ```
> 
> Remarquez qu’ avec la mise à jour ci-dessus, si l’application tente d’agir sur l’API de notification comme `chatAsyncClient.startRealtimeNotifications()` ou `chatAsyncClient.on()`, une erreur d’exécution se produit.


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Remplacez le commentaire `<ADD A USER>` par le code suivant :

```java
// The display name for the thread participant.
String secondUserDisplayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```


## <a name="list-users-in-a-thread"></a>Lister les utilisateurs dans un fil de conversation

Remplacez le commentaire `<LIST USERS>` par le code suivant (placez les instructions d’importation en haut du fichier) :

```java
import com.azure.android.core.rest.PagedResponse;
import com.azure.android.core.util.Context;

// The maximum number of participants to be returned per page, optional.
int maxPageSize = 10;

// Skips participants up to a specified position in response.
int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> getParticipantsFirstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant chatParticipant : getParticipantsFirstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(chatThreadAsyncClient, getParticipantsFirstPageWithResponse.getContinuationToken(), 2);

```

Insérez la méthode d’assistance suivante dans la classe `MainActivity` :

```java
void listParticipantsNextPage(ChatThreadAsyncClient chatThreadAsyncClient, String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        try {
            PagedResponse<ChatParticipant> nextPageWithResponse = chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
            for (ChatParticipant chatParticipant : nextPageWithResponse.getValue()) {
                // You code to handle participant
            }

            listParticipantsNextPage(chatThreadAsyncClient, nextPageWithResponse.getContinuationToken(), ++pageNumber);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Supprimer un utilisateur d’un fil de conversation

Nous allons supprimer le deuxième utilisateur du thread maintenant.

Remplacez le commentaire `<REMOVE A USER>` par le code suivant :

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Envoyer une notification de saisie

Remplacez le commentaire `<SEND A TYPING NOTIFICATION>` par le code suivant :

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Envoyer une confirmation de lecture

Nous allons anvoyer une confirmation de lecture pour le message envoyé ci-dessus.

Remplacez le commentaire `<SEND A READ RECEIPT>` par le code suivant :

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Répertorier les confirmations de lecture

Remplacez le commentaire `<READ RECEIPTS>` par le code suivant :

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> listReadReceiptsFirstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : listReadReceiptsFirstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(chatThreadAsyncClient, listReadReceiptsFirstPageWithResponse.getContinuationToken(), 2);

```

Insérez la méthode d’assistance suivante dans la classe :
```java
void listReadReceiptsNextPage(ChatThreadAsyncClient chatThreadAsyncClient, String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        try {
            PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
                    chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();
                    
            for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
                // You code to handle readReceipt
            }
            
            listParticipantsNextPage(chatThreadAsyncClient, nextPageWithResponse.getContinuationToken(), ++pageNumber);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```


## <a name="run-the-code"></a>Exécuter le code

Dans Android Studio, cliquez sur le bouton Run (Exécuter) pour générer et exécuter le projet. Dans la console, vous pouvez voir la sortie du code et la sortie de l’enregistreur d’événements du ChatClient.
