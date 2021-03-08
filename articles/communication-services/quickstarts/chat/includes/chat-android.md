---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 021abce5c6cd83257ad65f529833848d8f14f534
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750978"
---
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
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.6'
```

#### <a name="exclude-meta-files-in-packaging-options"></a>Exclure les fichiers méta dans les options d’empaquetage
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

Cliquez sur « Synchroniser maintenant » dans Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>(Alternative) Pour installer des bibliothèques via Maven
Pour importer la bibliothèque dans votre projet à l’aide du système de génération [Maven](https://maven.apache.org/), ajoutez-la à la section `dependencies` du fichier `pom.xml` de votre application, en spécifiant son ID d’artefact et la version que vous souhaitez utiliser :

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.6</version>
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
    private String second_user_id = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.6";
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
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient client = new ChatAsyncClient.Builder()
        .endpoint(endpoint)
        .credentialInterceptor(chain -> chain.proceed(chain.request()
                .newBuilder()
                .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                .build()))
        .build();
```

1. Utilisez le `ChatAsyncClient.Builder` pour configurer et créer une instance de `ChatAsyncClient`.
2. Remplacez `<resource>` par votre ressource Communication Services.
3. Remplacez `<user_access_token>` par un jeton d’accès Communication Services valide.

## <a name="object-model"></a>Modèle objet
Les classes et interfaces suivantes gèrent quelques-unes des principales fonctionnalités de la bibliothèque de client Azure Communication Services Chat pour JavaScript.

| Nom                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat). Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des threads. |
| ChatThreadClient/ChatThreadAsyncClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread). Vous obtenez une instance via ChatClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des utilisateurs, envoyer des notifications de saisie et des accusés de lecture, souscrire à des événements de conversation. |

## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

Nous allons utiliser notre `ChatClient` pour créer un thread avec un utilisateur initial.

Remplacez le commentaire `<CREATE A CHAT THREAD>` par le code suivant :

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
        .setId(id)
        .setDisplayName(displayName)
);

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
  .setTopic(topic)
  .setParticipants(participants);

// optional, set a repeat request ID
final String repeatabilityRequestID = "";

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
      public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
              ChatThread chatThread = result.getChatThread();
              threadId = chatThread.getId();
              // take further action
              Log.i(TAG, "threadId: " + threadId);
      }

      public void onFailure(Throwable throwable, okhttp3.Response response) {
              // Handle error.
              Log.e(TAG, throwable.getMessage());
      }
});
```

Remplacez `<user_id>` par un ID d’utilisateur Communication Services valide. Nous allons utiliser le `threadId` de la réponse renvoyée au gestionnaire d’achèvement dans les étapes ultérieures. Par conséquent, remplacez `<thread_id>` dans la classe par le `threadId` obtenu via cette demande, puis réexécutez l’application.

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation

Maintenant que nous avons créé un thread de conversation, nous allons obtenir un `ChatThreadClient` pour effectuer des opérations dans le thread. Remplacez le commentaire `<CREATE A CHAT THREAD CLIENT>` par le code suivant :

```
ChatThreadAsyncClient threadClient =
        new ChatThreadAsyncClient.Builder()
                .endpoint(endpoint)
                .credentialInterceptor(chain -> chain.proceed(chain.request()
                    .newBuilder()
                    .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                    .build()))
                .build();
```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Assurez-vous que nous avons remplacé `<thread_id>` par un ID de thread valide auquel nous allons envoyer le message maintenant.

Remplacez le commentaire `<SEND A MESSAGE>` par le code suivant :

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
        .setType(ChatMessageType.TEXT)
        .setContent(content)
        .setSenderDisplayName(senderDisplayName);

threadClient.sendChatMessage(threadId, message, new Callback<String>() {
        @Override
        public void onSuccess(String messageId, okhttp3.Response response) {
                // A string is the response returned from sending a message, it is an id,
                // which is the unique ID of the message.
                chatMessageId = messageId;
                // Take further action.
                Log.i(TAG, "chatMessageId: " + chatMessageId);
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Une fois que nous obtenons `chatMessageId`, nous pouvons remplacer `<chat_message_id>` par `chatMessageId` pour un usage ultérieur de la méthode dans un démarrage rapide, puis réexécuter l’application.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Remplacez le commentaire `<ADD A USER>` par le code suivant :

```java
//  The list of ChatParticipant to be added to the thread.
participants = new ArrayList<>();
// The display name for the thread participant.
displayName = "a new participant";
participants.add(new ChatParticipant().setId(second_user_id).setDisplayName(secondUserDisplayName));
// The model to pass to the add method.
AddChatParticipantsRequest addParticipantsRequest = new AddChatParticipantsRequest()
  .setParticipants(participants);

threadClient.addChatParticipants(threadId, addParticipantsRequest, new Callback<AddChatParticipantsResult>() {
        @Override
        public void onSuccess(AddChatParticipantsResult result, okhttp3.Response response) {
                // Take further action.
                Log.i(TAG, "add chat participants success");
        }
        
        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Remplacez `<second_user_id>` dans la classe par l’ID d’utilisateur Communication Services de l’utilisateur à ajouter. 

## <a name="list-users-in-a-thread"></a>Lister les utilisateurs dans un fil de conversation

Remplacez le commentaire `<LIST USERS>` par le code suivant :

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection,
            okhttp3.Response response) {
                // pageCollection enables enumerating list of chat participants.
                pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
                    @Override
                    public void onSuccess(Page<ChatParticipant> firstPage, okhttp3.Response response) {
                        for (ChatParticipant participant : firstPage.getItems()) {
                            // Take further action.
                            Log.i(TAG, "participant: " + participant.getDisplayName());
                        }
                        listChatParticipantsNext(firstPage.getNextPageId(), pageCollection);
                    }

                    @Override
                    public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                    }
                });

                @Override
                public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                }
                });
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Insérez la méthode d’assistance suivante dans la classe :

```java
void listChatParticipantsNext(String nextLink, AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
                for (ChatParticipant participant : nextPage.getItems()) {
                    // Take further action.
                    Log.i(TAG, "participant: " + participant.getDisplayName());
                }
                if (nextPage.getPageId() != null) {
                    listChatParticipantsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}
```


## <a name="remove-user-from-a-chat-thread"></a>Supprimer un utilisateur d’un fil de conversation

Veillez à remplacer `<second_user_id>` par un ID d’utilisateur valide. Nous allons supprimer le deuxième utilisateur du thread maintenant.

Remplacez le commentaire `<REMOVE A USER>` par le code suivant :

```java
threadClient.removeChatParticipant(threadId, second_user_id, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, okhttp3.Response response) {
        // Take further action.
        Log.i(TAG, "remove a user successfully");
    }

    @Override
    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-typing-notification"></a>Envoyer une notification de saisie

Remplacez le commentaire `<SEND A TYPING NOTIFICATION>` par le code suivant :

```java
threadClient.sendTypingNotification(threadId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a typing notification successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-read-receipt"></a>Envoyer une confirmation de lecture

Veillez à remplacer `<chat_message_id>` par un ID de message de conversation valide. Nous allons envoyer la confirmation de lecture pour ce message maintenant.

Remplacez le commentaire `<SEND A READ RECEIPT>` par le code suivant :

```java
SendReadReceiptRequest readReceipt = new SendReadReceiptRequest()
    .setChatMessageId(chatMessageId);
threadClient.sendChatReadReceipt(threadId, readReceipt, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a read receipt successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="list-read-receipts"></a>Répertorier les confirmations de lecture

Remplacez le commentaire `<READ RECEIPTS>` par le code suivant :

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
threadClient.listChatReadReceiptsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection,
                              Response response) {
            // pageCollection enables enumerating list of chat participants.
            pageCollection.getFirstPage(new Callback<Page<ChatMessageReadReceipt>>() {
                @Override
                public void onSuccess(Page<ChatMessageReadReceipt> firstPage, Response response) {
                    for (ChatMessageReadReceipt receipt : firstPage.getItems()) {
                        Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                    }
                    listChatReadReceiptsNext(firstPage.getNextPageId(), pageCollection);
                }

                @Override
                public void onFailure(Throwable throwable, Response response) {
                    Log.e(TAG, throwable.getMessage());
                }
            });
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            Log.e(TAG, throwable.getMessage());
        }
});
```

Insérez la méthode d’assistance suivante dans la classe :
```java
void listChatReadReceiptsNext(String nextLink, AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatMessageReadReceipt>>() {
            @Override
            public void onSuccess(Page<ChatMessageReadReceipt> nextPage, Response response) {
                for (ChatMessageReadReceipt receipt : nextPage.getItems()) {
                    Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                }
                if (nextPage.getPageId() != null) {
                    listChatReadReceiptsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}

```


## <a name="run-the-code"></a>Exécuter le code

Dans Android Studio, cliquez sur le bouton Run (Exécuter) pour générer et exécuter le projet. Dans la console, vous pouvez voir la sortie du code et la sortie de l’enregistreur d’événements du ChatClient.
