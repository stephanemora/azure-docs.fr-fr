---
title: Démarrage rapide - Participer à une réunion Teams
author: agiurg
ms.author: agiurg
ms.date: 07/20/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 07a8af0c389b0b5c2216f45e0a3fb0ba053e5690
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442885"
---
Dans ce démarrage rapide, vous allez découvrir comment rejoindre une conversation dans une réunion Teams à l’aide du Kit de développement logiciel (SDK) Conversation Azure Communication Services pour Android.

## <a name="prerequisites"></a>Prérequis 

-  [Déploiement Teams](/deployoffice/teams-install). 
- Une [application d’appel](../../voice-video-calling/get-started-teams-interop.md) opérationnelle. 

## <a name="enable-teams-interoperability"></a>Activer l’interopérabilité de Teams 

Un utilisateur Communication Services qui rejoint une réunion Teams en tant qu’utilisateur invité ne peut accéder à la conversation de la réunion qu’après avoir rejoint l’appel de réunion Teams. Consultez la documentation [Interopérabilité de Teams](../../voice-video-calling/get-started-teams-interop.md) pour savoir comment ajouter un utilisateur Communication Services à un appel de réunion Teams.

Vous devez être membre de l’organisation propriétaire des deux entités pour pouvoir utiliser cette fonctionnalité.

## <a name="joining-the-meeting-chat"></a>Participation à la conversation d’une réunion 

Une fois l’interopérabilité de Teams activée, un utilisateur de Communication Services peut rejoindre l’appel Teams en tant qu’utilisateur externe à l’aide du kit de développement logiciel (SDK) Calling. En rejoignant l’appel, il est également ajouté comme participant à la conversation de la réunion, où il peut échanger des messages avec d’autres utilisateurs lors de l’appel. L’utilisateur n’aura pas accès aux messages de conversation qui ont été envoyés avant qu’il ne rejoigne l’appel. Pour participer à la réunion et commencer à converser, vous pouvez suivre les étapes suivantes.

## <a name="add-chat-to-the-teams-calling-app"></a>Ajouter la conversation à l’application d’appel Teams

Dans votre fichier build.gradle au niveau du module, ajoutez la dépendance sur le Kit de développement logiciel (SDK) de conversation.

> [!IMPORTANT]
> Problème connu : lors de l’utilisation conjointe du chat Android et du Kit de développement logiciel (SDK) d’appel dans la même application, la fonctionnalité de notifications en temps réel du SDK de conversation ne fonctionne pas. Vous obtiendrez un problème de résolution de dépendance. Pendant que nous travaillons sur une solution, vous pouvez désactiver la fonctionnalité de notifications en temps réel en ajoutant les exclusions suivantes à la dépendance du Kit de développement logiciel (SDK) de conversation dans le fichier `build.gradle` de l’application :
> 
> ```groovy
> implementation ("com.azure.android:azure-communication-chat:1.0.0") {
>     exclude group: 'com.microsoft', module: 'trouter-client-android'
> }
> ```


## <a name="add-the-teams-ui-layout"></a>Ajouter la disposition de l’interface utilisateur Teams

Remplacez le code dans activity_main.xml par l’extrait de code suivant. Il ajoute des entrées pour l’ID de thread et pour l’envoi de messages, un bouton pour envoyer le message saisi et une disposition de base pour le chat.

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_thread_id"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="128dp"
        android:ems="10"
        android:hint="Meeting Thread Id"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="64dp"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:id="@+id/button_layout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="30dp"
        android:gravity="center"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.0"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/teams_meeting_thread_id">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <ScrollView
        android:id="@+id/chat_box"
        android:layout_width="374dp"
        android:layout_height="294dp"
        android:layout_marginTop="40dp"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toTopOf="@+id/send_message_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/button_layout"
        android:orientation="vertical"
        android:gravity="bottom"
        android:layout_gravity="bottom"
        android:fillViewport="true">

        <LinearLayout
            android:id="@+id/chat_box_layout"
            android:orientation="vertical"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:gravity="bottom"
            android:layout_gravity="top"
            android:layout_alignParentBottom="true"/>
    </ScrollView>

    <EditText
        android:id="@+id/message_body"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginHorizontal="20dp"
        android:layout_marginTop="588dp"
        android:ems="10"
        android:inputType="textUri"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:text="Type your message here..."
        tools:visibility="invisible" />

    <Button
        android:id="@+id/send_message_button"
        android:layout_width="138dp"
        android:layout_height="45dp"
        android:layout_marginStart="133dp"
        android:layout_marginTop="48dp"
        android:layout_marginEnd="133dp"
        android:text="Send Message"
        android:visibility="invisible"
        app:layout_constraintBottom_toTopOf="@+id/recording_status_bar"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.428"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/chat_box" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>Activer les contrôles d’interface utilisateur de Teams

### <a name="import-packages-and-define-state-variables"></a>Importer des packages et définir des variables d’état

Ajoutez les importations supplémentaires suivantes au contenu de `MainActivity.java` :

```
import android.graphics.Typeface;
import android.graphics.Color;
import android.text.Html;
import android.os.Handler;
import android.view.Gravity;
import android.view.View;
import android.widget.LinearLayout;
import java.util.Collections;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import com.azure.android.communication.chat.ChatThreadAsyncClient;
import com.azure.android.communication.chat.ChatThreadClientBuilder;
import com.azure.android.communication.chat.models.ChatMessage;
import com.azure.android.communication.chat.models.ChatMessageType;
import com.azure.android.communication.chat.models.ChatParticipant;
import com.azure.android.communication.chat.models.ListChatMessagesOptions;
import com.azure.android.communication.chat.models.SendChatMessageOptions;
import com.azure.android.communication.common.CommunicationIdentifier;
import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.core.rest.util.paging.PagedAsyncStream;
import com.azure.android.core.util.AsyncStreamHandler;
```

Ajoutez les variables suivantes à la classe `MainActivity` :

```
    // InitiatorId is used to differentiate incoming messages from outgoing messages
    private static final String InitiatorId = "<USER_ID>";
    private static final String ResourceUrl = "<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>";
    private String threadId;
    private ChatThreadAsyncClient chatThreadAsyncClient;
    
    // The list of ids corresponsding to messages which have already been processed
    ArrayList<String> chatMessages = new ArrayList<>();
```

Remplacez `<USER_ID>` par l’ID de l’utilisateur qui lance la conversation.
Remplacez `<COMMUNICATION_SERVICES_RESOURCE_ENDPOINT>` par le point de terminaison de votre ressource Communication Services. 

### <a name="initialize-the-chatthreadclient"></a>Initialiser le ChatThreadClient

Après avoir rejoint la réunion, instanciez le `ChatThreadClient` et rendez visibles les composants du chat.

Mettez à jour la fin de la méthode `MainActivity.joinTeamsMeeting()` avec le code ci-dessous :

```
    private void joinTeamsMeeting() {
        ...
        EditText threadIdView = findViewById(R.id.teams_meeting_thread_id);
        threadId = threadIdView.getText().toString();
        // Initialize Chat Thread Client
        chatThreadAsyncClient = new ChatThreadClientBuilder()
                .endpoint(ResourceUrl)
                .credential(new CommunicationTokenCredential(UserToken))
                .chatThreadId(threadId)
                .buildAsyncClient();
        Button sendMessageButton = findViewById(R.id.send_message_button);
        EditText messageBody = findViewById(R.id.message_body);
        // Register the method for sending messages and toggle the visibility of chat components
        sendMessageButton.setOnClickListener(l -> sendMessage());
        sendMessageButton.setVisibility(View.VISIBLE);
        messageBody.setVisibility(View.VISIBLE);
        
        // Start the polling for chat messages immediately
        handler.post(runnable);
    }
```

### <a name="enable-sending-messages"></a>Activer l’envoi des messages

Ajoutez la méthode `sendMessage()` à `MainActivity`. Elle utilisera le `ChatThreadClient` pour envoyer des messages au nom de l’utilisateur.

```
    private void sendMessage() {
        // Retrieve the typed message content
        EditText messageBody = findViewById(R.id.message_body);
        // Set request options and send message
        SendChatMessageOptions options = new SendChatMessageOptions();
        options.setContent(messageBody.getText().toString());
        options.setSenderDisplayName("ACS User");
        chatThreadAsyncClient.sendMessage(options);
        // Clear the text box
        messageBody.setText("");
    }
```

### <a name="enable-polling-for-messages-and-rendering-them-in-the-application"></a>Activer l’interrogation des messages et leur rendu dans l’application

> [!IMPORTANT]
> Problème connu : Dans la mesure où la fonctionnalité de notifications en temps réel du Kit de développement logiciel (SDK) de conversation ne fonctionne pas avec le SDK d’appel, nous devons interroger l’API `GetMessages` à des intervalles prédéfinis. Dans notre exemple, nous allons utiliser des intervalles de trois secondes.

Nous pouvons obtenir les données suivantes à partir de la liste des messages renvoyée par l’API `GetMessages` : 
 - Les messages `text` et `html` sur la conversation depuis qu’elle a été rejointe
 - Les modifications apportées à la liste des participants à la conversation
 - Les mises à jour du sujet de la conversation


Ajoutez à la classe `MainActivity` un gestionnaire et une tâche exécutable qui seront exécutés à intervalles de trois secondes :

```
    private Handler handler = new Handler();
    private Runnable runnable = new Runnable() {
        @Override
        public void run() {
            try {
                retrieveMessages();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            // Repeat every 3 seconds
            handler.postDelayed(runnable, 3000);
        }
    };
```

Notez que la tâche a déjà été démarrée à la fin de la méthode `MainActivity.joinTeamsMeeting()` mise à jour dans l’étape d’initialisation.

Enfin, nous allons ajouter la méthode permettant d’interroger tous les messages accessibles sur la conversation, de les analyser par type de message et d’afficher les messages `html` et `text` :

```
    private void retrieveMessages() throws InterruptedException {
        // Initialize the list of messages not yet processed
        ArrayList<ChatMessage> newChatMessages = new ArrayList<>();
        
        // Retrieve all messages accessible to the user
        PagedAsyncStream<ChatMessage> messagePagedAsyncStream
                = this.chatThreadAsyncClient.listMessages(new ListChatMessagesOptions(), null);
        // Set up a lock to wait until all returned messages have been inspected
        CountDownLatch latch = new CountDownLatch(1);
        // Traverse the returned messages
        messagePagedAsyncStream.forEach(new AsyncStreamHandler<ChatMessage>() {
            @Override
            public void onNext(ChatMessage message) {
                // Messages that should be displayed in the chat
                if ((message.getType().equals(ChatMessageType.TEXT)
                    || message.getType().equals(ChatMessageType.HTML))
                    && !chatMessages.contains(message.getId())) {
                    newChatMessages.add(message);
                    chatMessages.add(message.getId());
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_ADDED)) {
                    // Handle participants added to chat operation
                    List<ChatParticipant> participantsAdded = message.getContent().getParticipants();
                    CommunicationIdentifier participantsAddedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.PARTICIPANT_REMOVED)) {
                    // Handle participants removed from chat operation
                    List<ChatParticipant> participantsRemoved = message.getContent().getParticipants();
                    CommunicationIdentifier participantsRemovedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
                if (message.getType().equals(ChatMessageType.TOPIC_UPDATED)) {
                    // Handle topic updated
                    String newTopic = message.getContent().getTopic();
                    CommunicationIdentifier topicUpdatedBy = message.getContent().getInitiatorCommunicationIdentifier();
                }
            }
            @Override
            public void onError(Throwable throwable) {
                latch.countDown();
            }
            @Override
            public void onComplete() {
                latch.countDown();
            }
        });
        // Wait until the operation completes
        latch.await(1, TimeUnit.MINUTES);
        // Returned messages should be ordered by the createdOn field to be guaranteed a proper chronological order
        // For the purpose of this demo we will just reverse the list of returned messages
        Collections.reverse(newChatMessages);
        for (ChatMessage chatMessage : newChatMessages)
        {
            LinearLayout chatBoxLayout = findViewById(R.id.chat_box_layout);
            // For the purpose of this demo UI, we don't need to use HTML formatting for displaying messages
            // The Teams client always sends html messages in meeting chats 
            String message = Html.fromHtml(chatMessage.getContent().getMessage(), Html.FROM_HTML_MODE_LEGACY).toString().trim();
            TextView messageView = new TextView(this);
            messageView.setText(message);
            // Compare with sender identifier and align LEFT/RIGHT accordingly
            // ACS users are of type CommunicationUserIdentifier
            CommunicationIdentifier senderId = chatMessage.getSenderCommunicationIdentifier();
            if (senderId instanceof CommunicationUserIdentifier
                && InitiatorId.equals(((CommunicationUserIdentifier) senderId).getId())) {
                messageView.setTextColor(Color.GREEN);
                messageView.setGravity(Gravity.RIGHT);
            } else {
                messageView.setTextColor(Color.BLUE);
                messageView.setGravity(Gravity.LEFT);
            }
            // Note: messages with the deletedOn property set to a timestamp, should be marked as deleted
            // Note: messages with the editedOn property set to a timestamp, should be marked as edited
            messageView.setTypeface(Typeface.SANS_SERIF, Typeface.BOLD);
            chatBoxLayout.addView(messageView);
        }
    }
```

Les noms d’affichage des participants aux conversations ne sont pas définis par le client Teams. La valeur Null est renvoyée dans l’API pour la liste des participants, dans l’événement `participantsAdded` et dans l’événement `participantsRemoved`. Les noms d’affichage des participants à la conversation peuvent être récupérés dans le champ `remoteParticipants` de l’objet `call`.

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Obtenir le fil de conversation d’une réunion Teams pour un utilisateur Communication Services

Il est possible récupérer le lien et la conversation de la réunion Teams à l’aide d’API Graph détaillées dans la [Documentation Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true). Le kit SDK Communication Services Calling accepte un lien de réunion Teams complet. Ce lien est renvoyé avec la ressource `onlineMeeting`, accessible sous la propriété [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Avec les [API Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true), vous pouvez également obtenir le `threadId`. La réponse aura un objet `chatInfo` contenant le `threadID`. 

Vous pouvez également récupérer les informations de réunion et l’ID de thread requis à partir de l’URL **Rejoindre la réunion** disponible dans l’invitation à la réunion Teams proprement dite.
Un lien de réunion Teams ressemble à ceci : `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. Le `threadId` sera là où `meeting_chat_thread_id` se trouve dans le lien. Assurez-vous que le `meeting_chat_thread_id` n’est pas échappé avant de l’utiliser. Il doit être au format suivant : `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Exécuter le code

L’application peut maintenant être lancée à l’aide du bouton « Run App » de la barre d’outils (Maj+F10).

Pour rejoindre la conversation et la réunion Teams, entrez le lien de réunion et l’ID de conversation de votre équipe dans l’interface utilisateur.

Une fois que vous avez rejoint la réunion de l’équipe, vous devez autoriser l’utilisateur à participer à la réunion dans le client de votre équipe. Une fois que l’utilisateur est admis et a rejoint la conversation, vous pouvez envoyer et recevoir des messages.

:::image type="content" source="../join-teams-meeting-chat-quickstart-android.png" alt-text="Capture d’écran de l’application Android terminée.":::

> [!NOTE] 
> Actuellement, seuls l’envoi, la réception et la modification de messages sont pris en charge pour les scénarios d’interopérabilité avec Teams. D’autres fonctionnalités, telles que les indicateurs de saisie et les utilisateurs Communication Services qui ajoutent ou suppriment d’autres utilisateurs dans la réunion Teams, ne sont pas prises en charge pour l’instant.
