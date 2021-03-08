---
title: Fichier include
description: Fichier include
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 0225c948fddf65b9312c689144ecc567a70aa27e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750986"
---
## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous de :

- Créer un compte Azure avec un abonnement actif. Pour plus d’informations, consultez [Créer un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Installez [Python](https://www.python.org/downloads/)
- Créer une ressource Azure Communication Services. Pour plus d’informations, consultez [Créer une ressource Azure Communication](../../create-communication-resource.md). Vous devrez inscrire le **point de terminaison** de votre ressource pour ce démarrage rapide.
- [Jeton d’accès utilisateur](../../access-tokens.md). Veillez à définir l’étendue sur « chat » (conversation) et notez la chaîne token ainsi que la chaîne userId.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-python-application"></a>Créer une application Python

Ouvrez votre fenêtre de terminal ou de commande, créez un répertoire pour votre application, puis accédez-y.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Utilisez un éditeur de texte pour créer un fichier appelé **send-chat.py** dans le répertoire racine du projet, puis ajoutez la structure du programme, notamment la gestion des exceptions de base. Dans les sections suivantes, vous ajouterez à ce fichier l’ensemble du code source de ce démarrage rapide.

```python
import os
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Installer la bibliothèque de client

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent quelques-unes des principales fonctionnalités de la bibliothèque de client Azure Communication Services Chat pour Python.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Cette classe est nécessaire à la fonctionnalité de conversation (Chat). Vous l’instanciez avec vos informations d’abonnement et l’utilisez pour créer, obtenir et supprimer des fils de conversation. |
| ChatThreadClient | Cette classe est nécessaire à la fonctionnalité de fil de conversation (Chat Thread). Vous obtenez une instance via ChatClient et l’utilisez pour envoyer/recevoir/mettre à jour/supprimer des messages, ajouter/supprimer/obtenir des utilisateurs, envoyer des notifications de saisie et des accusés de lecture. |

## <a name="create-a-chat-client"></a>Créer un client de conversation

Pour créer un client de conversation, vous allez utiliser le point de terminaison Communication Services ainsi que le `Access Token` qui a été généré au cours des étapes prérequises. Apprenez-en davantage sur les [jetons d’accès utilisateur](../../access-tokens.md).

Ce guide de démarrage rapide ne couvre pas la création d’un niveau de service pour gérer les jetons de votre application de conversation, bien que cette opération soit recommandée. Consultez la documentation suivante pour plus d’informations sur l’[Architecture de conversation](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Démarrer un fil de conversation

Utilisez la méthode `create_chat_thread` pour créer un fil de conversation.

- Utilisez `topic` pour attribuer un sujet au fil ; le sujet peut être mis à jour après que le fil de conversation a été créé à l’aide de la fonction `update_thread`.
- Utilisez `thread_participants` pour lister les `ChatThreadParticipant` à ajouter au fil de conversation. `ChatThreadParticipant` prend le type `CommunicationUserIdentifier` comme `user`, que vous avez obtenu à l’issue du processus de création décrit dans [Créer un utilisateur](../../access-tokens.md#create-an-identity)
- Utilisez `repeatability_request_id` pour indiquer que la requête est renouvelable. Le client peut effectuer la requête plusieurs fois avec le même identifiant Repeatability-Request-ID et obtenir une réponse appropriée sans que le serveur exécute la requête plusieurs fois.

La réponse `chat_thread_client` est utilisée pour effectuer des opérations sur le fil de conversation nouvellement créé : ajout de participants au fil de conversation, envoi d’un message, suppression d’un message, etc. Elle contient une propriété `thread_id` qui est l’ID unique du fil de conversation.

#### <a name="without-repeatability-request-id"></a>Sans identifiant Repeatability-Request-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>Avec identifiant Repeatability-Request-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Obtenir un client de fil de conversation
La méthode `get_chat_thread_client` retourne un client de fil pour un fil qui existe déjà. Ce client peut être utilisé pour effectuer des opérations sur le fil de conversation créé : ajout de participants, envoi d’un message, etc. thread_id est l’identifiant unique du fil de conversation existant.

```python
thread_id = chat_thread_client.thread_id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>Répertorier tous les fils de conversation
La méthode `list_chat_threads` retourne un itérateur de type `ChatThreadInfo`. Celui-ci peut être utilisé pour répertorier tous les fils de conversation.

- Utilisez `start_time` pour spécifier l’instant dans le passé le plus ancien auquel vous souhaitez accéder pour obtenir les fils de conversation.
- Utilisez `results_per_page` pour spécifier le nombre maximal de fils de conversation retournés par page.

```python
from datetime import datetime, timedelta
import pytz

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        # Iterate over all chat threads
        print("thread id:", chat_thread_info.id)
```

## <a name="delete-a-chat-thread"></a>Supprimer un fil de conversation
`delete_chat_thread` est utilisé pour supprimer un fil de conversation.

- Utilisez `thread_id` pour spécifier l’identifiant thread_id d’un fil de conversation existant qui doit être supprimé

```python
thread_id = chat_thread_client.thread_id
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Envoyer un message à un fil de conversation

Utilisez la méthode `send_message` pour envoyer un message au fil de conversation que vous venez de créer, identifié par thread_id.

- Utilisez `content` pour fournir le contenu du message de conversation ;
- Utilisez `chat_message_type` pour spécifier le type de contenu du message. Les valeurs possibles sont « text » et « html ». Si aucune valeur n’est spécifiée, la valeur par défaut « text » est attribuée.
- Utilisez `sender_display_name` pour spécifier le nom d’affichage de l’expéditeur ;

La réponse est un identifiant « id » de type `str` qui est l’unique identifiant de ce message.

#### <a name="message-type-not-specified"></a>Type de message non spécifié
```python
chat_thread_client = chat_client.create_chat_thread(topic, participants)

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>Type de message spécifié
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Obtenir un message de conversation spécifique à partir d’un fil de conversation
La fonction `get_message` peut être utilisée pour récupérer un message spécifique, identifié par un message_id

- Utilisez `message_id` pour spécifier l’identifiant du message.

La réponse de type `ChatMessage` contient toutes les informations relatives au message unique.

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Recevoir les messages de conversation d’un fil de conversation

Vous pouvez récupérer les messages de conversation en interrogeant la méthode `list_messages` selon des intervalles définis.

- Utilisez `results_per_page` pour spécifier le nombre maximal de messages retournés par page.
- Utilisez `start_time` pour spécifier l’instant dans le passé le plus ancien auquel vous souhaitez accéder pour obtenir les messages.

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print('ChatMessage: ', chat_message)
        print('ChatMessage: ', chat_message.content.message)
```

`list_messages` retourne la version la plus récente du message, avec les modifications ou les suppressions dont le message a éventuellement fait l’objet via `update_message` et `delete_message`. Pour les messages supprimés, `ChatMessage.deleted_on` retourne une valeur datetime indiquant à quel moment ce message a été supprimé. Pour les messages modifiés, `ChatMessage.edited_on` retourne une valeur datetime indiquant à quel moment ce message a été modifié. Il est possible d’accéder à l’heure initiale de création du message à l’aide de `ChatMessage.created_on` qui peut être utilisée à des fins de classement des messages.

`list_messages` retourne différents types de messages qui peuvent être identifiés par `ChatMessage.type`. Ces types sont les suivants :

- `ChatMessageType.TEXT` : Message de conversation ordinaire envoyé par un participant au fil de conversation.

- `ChatMessageType.HTML` : message de conversation envoyé par un participant au fil de conversation.

- `ChatMessageType.TOPIC_UPDATED`: Message système qui indique que le sujet a été mis à jour

- `ChatMessageType.PARTICIPANT_ADDED` : Message système qui indique qu’un ou plusieurs participants ont été ajoutés au fil de conversation.

- `ChatMessageType.PARTICIPANT_REMOVED` : Message système qui indique qu’un participant a été supprimé du fil de conversation.

Pour plus d’informations, consultez [Types de messages](../../../concepts/chat/concepts.md#message-types).

## <a name="update-topic-of-a-chat-thread"></a>Mettre à jour le sujet d’un fil de conversation
Vous pouvez mettre à jour le sujet d’un fil de conversation à l’aide de la méthode `update_topic`

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)
updated_topic = chat_client.get_chat_thread(chat_thread_client.thread_id).topic
print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Mettre à jour un message
Vous pouvez mettre à jour le contenu d’un message existant à l’aide de la méthode `update_message`, identifiée par le message_id

- Utiliser `message_id` pour spécifier le message_id
- Utiliser `content` pour définir le nouveau contenu du message

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>Envoyer une confirmation de lecture pour un message
La méthode `send_read_receipt` peut être utilisée pour publier un événement de confirmation de lecture sur un fil de conversation, pour le compte d’un utilisateur.

- Utilisez `message_id` pour spécifier l’identifiant du dernier message lu par l’utilisateur actuel.

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Répertorier les confirmations de lecture d’un fil de conversation
La méthode `list_read_receipts` peut être utilisée pour obtenir des confirmations de lecture pour un fil de conversation.

- Utilisez `results_per_page` pour spécifier le nombre maximal de confirmations de lecture de message de conversation retournées par page.
- Utilisez `skip` pour spécifier les accusés de lecture de message de conversation ignorés jusqu’à une position spécifiée dans la réponse.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
```

## <a name="send-typing-notification"></a>Envoyer une notification de saisie
La méthode `send_typing_notification` peut être utilisée pour publier un événement de saisie sur un fil de conversation, pour le compte d’un utilisateur.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Supprimer un message
La méthode `delete_message` peut être utilisée pour supprimer un message, identifié par un message_id

- Utiliser `message_id` pour spécifier le message_id

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Ajouter un utilisateur comme participant au fil de conversation

Une fois qu’un fil de conversation est créé, vous pouvez y ajouter des utilisateurs et en supprimer. En ajoutant des utilisateurs, vous leur octroyez l’accès permettant d’envoyer des messages au fil de conversation et d’ajouter ou de supprimer d’autres participants. Avant d’appeler la méthode `add_participant`, vérifiez que vous avez acquis un nouveau jeton d’accès et une identité pour cet utilisateur. L’utilisateur aura besoin de ce jeton d’accès pour initialiser son client de conversation.

Utilisez la méthode `add_participant` pour ajouter des participants au fil de conversation identifié par thread_id.

- Utilisez `thread_participant` pour spécifier les participants à ajouter au fil de conversation ;
- `user`, obligatoire, est le `CommunicationUserIdentifier` que vous avez créé avec `CommunicationIdentityClient` à l’étape [Créer un utilisateur](../../access-tokens.md#create-an-identity).
- `display_name`, facultatif, est le nom d’affichage pour le participant au fil.
- `share_history_time`, facultatif, est le moment à partir duquel l’historique de conversation est partagé avec le participant. Pour partager l’historique depuis le début du fil de conversation, attribuez à cette propriété une date égale ou antérieure à la date de création du fil. Pour ne pas partager l’historique antérieur au moment où le participant a été ajouté, définissez-la sur l’heure actuelle. Pour partager un historique partiel, attribuez-lui une date intermédiaire.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

Plusieurs utilisateurs peuvent également être ajoutés au fil de conversation à l’aide de la méthode `add_participants`, à condition qu’un nouveau jeton d’accès et qu’une nouvelle identification soient disponibles pour tous les utilisateurs.

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>Supprimer un utilisateur d’un fil de conversation

De la même façon que vous ajoutez un participant, vous pouvez également supprimer des participants d’un fil de conversation. Pour pouvoir effectuer une suppression, vous devez suivre les ID des participants que vous avez ajoutés.

Utilisez la méthode `remove_participant` pour supprimer des participants du fil de conversation identifié par threadId.
- `user` correspond à l’élément `CommunicationUserIdentifier` à supprimer du fil de conversation.

```python
chat_thread_client.remove_participant(new_user)
```

## <a name="run-the-code"></a>Exécuter le code

Exécutez l’application à partir de votre répertoire d’application avec la commande `python`.

```console
python start-chat.py
```
