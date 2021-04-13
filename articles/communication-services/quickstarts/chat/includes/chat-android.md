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
ms.openlocfilehash: b2c5237f3f7e949edbfb5486a3a17cc6e0a008a4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178252"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez [Android Studio](https://developer.android.com/studio) que nous allons utiliser afin de créer une application Android pour le démarrage rapide des l’installation dépendances.
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer une ressource Azure Communication](../../create-communication-resource.md). Vous devrez **enregistrer le point de terminaison de votre ressource** pour ce guide de démarrage rapide.
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

Ouvrez et modifiez le fichier `MainActivity.java`. Dans ce démarrage rapide, nous allons ajouter notre code à `MainActivity`, puis afficher la sortie dans la console. Ce démarrage rapide ne traite pas de la génération d’une interface utilisateur. En haut du fichier, importez les bibliothèques `Communication common` et `Communication chat` :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Copiez le code suivant dans le fichier `MainActivity` :

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

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

Dans les étapes suivantes, nous allons remplacer les espaces réservés par un exemple de code en utilisant la bibliothèque Azure Communication Services Chat.


### <a name="create-a-chat-client"></a>Créer un client de conversation

Remplacez le commentaire `<CREATE A CHAT CLIENT>` par le code suivant (placez les instructions d’importation en haut du fichier) :

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. Utilisez le `ChatClientBuilder` pour configurer et créer une instance de `ChatAsyncClient`.
2. Remplacez `<resource>` par votre ressource Communication Services.
3. Remplacez `<user_access_token>` par un jeton d’accès Communication Services valide.

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
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
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

Remplacez `<user_id>` par un ID d’utilisateur Communication Services valide. Nous allons utiliser le `threadId` de la réponse renvoyée au gestionnaire d’achèvement dans les étapes ultérieures. Par conséquent, remplacez `<thread_id>` dans la classe par le `threadId` obtenu via cette demande, puis réexécutez l’application.

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation

Maintenant que nous avons créé un thread de conversation, nous allons obtenir un `ChatThreadAsyncClient` pour effectuer des opérations dans le thread. Remplacez le commentaire `<CREATE A CHAT THREAD CLIENT>` par le code suivant :

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Assurez-vous que nous avons remplacé `<thread_id>` par un ID de thread valide auquel nous allons envoyer le message maintenant.

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

Une fois que nous obtenons `chatMessageId`, nous pouvons remplacer `<chat_message_id>` par `chatMessageId` pour un usage ultérieur de la méthode dans un démarrage rapide, puis réexécuter l’application.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Remplacez le commentaire `<ADD A USER>` par le code suivant :

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

Remplacez `<second_user_id>` dans la classe par l’ID d’utilisateur Communication Services de l’utilisateur à ajouter. 

## <a name="list-users-in-a-thread"></a>Lister les utilisateurs dans un fil de conversation

Remplacez le commentaire `<LIST USERS>` par le code suivant :

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Insérez la méthode d’assistance suivante dans la classe :

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Supprimer un utilisateur d’un fil de conversation

Veillez à remplacer `<second_user_id>` par un ID d’utilisateur valide. Nous allons supprimer le deuxième utilisateur du thread maintenant.

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

Veillez à remplacer `<chat_message_id>` par un ID de message de conversation valide. Nous allons envoyer la confirmation de lecture pour ce message maintenant.

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

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Insérez la méthode d’assistance suivante dans la classe :
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>Exécuter le code

Dans Android Studio, cliquez sur le bouton Run (Exécuter) pour générer et exécuter le projet. Dans la console, vous pouvez voir la sortie du code et la sortie de l’enregistreur d’événements du ChatClient.
