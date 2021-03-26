---
title: Utilisation du Stockage File d’attente (C++) - Stockage Azure
description: Découvrez comment utiliser le service Stockage File d’attente Azure. Les exemples sont écrits en C++.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/16/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 44d64c54049c02b6602f01b97effcc33b03dbcfe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591325"
---
# <a name="how-to-use-queue-storage-from-c"></a>Utilisation du stockage de files d'attente à partir de C++

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service Stockage File d’attente Azure. Les exemples ont été écrits en C++ et utilisent la [bibliothèque de client Stockage Azure pour C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Les scénarios traités incluent **l’insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d’attente, ainsi que la **création et suppression des files d’attente**.

> [!NOTE]
> Ce guide cible la bibliothèque de client Stockage Azure pour C++ v1.0.0 et versions ultérieures. La version recommandée pour la bibliothèque de client Stockage Azure est la version 2.2.0, qui est disponible sur [NuGet](https://www.nuget.org/packages/wastorage) et [GitHub](https://github.com/Azure/azure-storage-cpp/).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Création d’une application C++

Dans ce guide, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application C++.

Pour ce faire, vous devez installer la bibliothèque de client Stockage Azure pour C++ et créer un compte Stockage Azure dans votre abonnement Azure.

<!-- docutune:casing "Getting Started on Linux" -->

Si vous souhaitez installer la bibliothèque de client Stockage Azure pour C++, vous pouvez utiliser les méthodes suivantes :

- **Linux :** Suivez les instructions disponibles dans [Bibliothèque de client Stockage Azure pour C++ README : Prise en main de la page Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux).
- **Windows :** Sous Windows, utilisez [vcpkg](https://github.com/microsoft/vcpkg) en tant que manager de dépendances. Suivez le [guide de démarrage rapide](https://github.com/microsoft/vcpkg#quick-start) pour initialiser `vcpkg`. Puis, pour installer la bibliothèque, utilisez la commande suivante :

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Vous trouverez un guide sur la façon de générer le code source et de l’exporter vers NuGet dans le fichier [LISEZ-MOI](https://github.com/Azure/azure-storage-cpp#download--install).

## <a name="configure-your-application-to-access-queue-storage"></a>Configuration de votre application pour accéder au stockage de files d'attente

Ajoutez les instructions d’importation suivantes au début du fichier Java dans lequel vous voulez utiliser des API Stockage Azure pour accéder aux files d’attente :

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurer une chaîne de connexion au stockage Azure

Un client de stockage Azure utilise une chaîne de connexion au stockage pour stocker des points de terminaison et des informations d’identification permettant d’accéder aux services de gestion des données. Lors de l’exécution d’une application cliente, vous devez spécifier la chaîne de connexion au stockage au format suivant, en indiquant le nom de votre compte de stockage et sa clé d’accès de stockage, correspondant aux valeurs `AccountName` et `AccountKey`, sur le [portail Azure](https://portal.azure.com). Pour plus d’informations sur les comptes de stockage et les clés d’accès, consultez [À propos des comptes de stockage Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Cet exemple vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion :

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Pour tester votre application sur votre ordinateur Windows local, vous pouvez utiliser [l’émulateur de stockage Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Azurite est un utilitaire qui simule sur votre ordinateur de développement local les services Stockage Blob Azure et Stockage File d’attente. L’exemple suivant vous montre comment déclarer un champ statique pour qu’il contienne une chaîne de connexion vers votre émulateur de stockage local :

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pour démarrer Azurite, consultez [Utiliser l’émulateur Azurite à des fins de développement local pour Stockage Azure](../common/storage-use-azurite.md).

Les exemples ci-dessous partent du principe que vous avez utilisé l’une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## <a name="retrieve-your-connection-string"></a>Récupération de votre chaîne de connexion

Vous pouvez utiliser la classe `cloud_storage_account` pour représenter les informations de votre compte de stockage. Pour récupérer les informations de votre compte de stockage à partir de la chaîne de connexion de stockage, vous pouvez utiliser la méthode `parse`.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Procédure : Créer une file d’attente

Un objet `cloud_queue_client` vous permet d'obtenir les objets de référence pour les files d'attente. Le code suivant crée un objet `cloud_queue_client`.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Utilisez l'objet `cloud_queue_client` pour obtenir une référence pointant vers la file d'attente à utiliser. Si la file d'attente n'existe pas, vous pouvez la créer :

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procédure : Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d’attente existante, commencez par créer un `cloud_queue_message`. Appelez ensuite la méthode `add_message`. Vous pouvez créer un `cloud_queue_message` à partir d’une chaîne (au format UTF-8) ou d’un tableau d’octets. Voici le code qui crée une file d’attente (si elle n’existe pas déjà) et insère le message `Hello, World` :

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Procédure : Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d’attente sans l’enlever de la file d’attente en appelant la méthode `peek_message`.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procédure : Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code suivant met à jour le message de la file d'attente avec un nouveau contenu et ajoute 60 secondes au délai d'expiration de la visibilité. Cette opération enregistre l'état de la tâche associée au message et accorde une minute supplémentaire au client pour traiter le message. Vous pouvez utiliser cette technique pour suivre des workflows à plusieurs étapes sur les messages de file d’attente, sans devoir reprendre du début si une étape du traitement échoue à cause d’une défaillance matérielle ou logicielle. Normalement, vous conservez aussi un nombre de nouvelles tentatives et si le message est retenté plus de n fois, vous le supprimez. Cela protège du déclenchement d'une erreur d'application par un message chaque fois qu'il est traité.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-dequeue-the-next-message"></a>Procédure : Suppression du message suivant de la file d’attente

Votre code enlève un message d'une file d'attente en deux étapes. Lorsque vous appelez `get_message`, vous obtenez le message suivant dans une file d'attente. Un message renvoyé par `get_message` devient invisible par les autres codes lisant les messages de cette file d'attente. Pour finaliser la suppression du message de la file d’attente, vous devez aussi appeler `delete_message`. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle `delete_message` juste après le traitement du message.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-use-additional-options-for-dequeuing-messages"></a>Procédure : Utiliser des options supplémentaires pour retirer des messages de la file d’attente

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode `get_messages` pour obtenir 20 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle `for`. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message. Notez que le délai de 5 minutes démarre en même temps pour tous les messages. Par conséquent, une fois les 5 minutes écoulées après l’appel de `get_messages`, tous les messages n’ayant pas été supprimés redeviennent visibles.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Procédure : Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode `download_attributes` retourne les propriétés de file d’attente, y compris le nombre de messages. La méthode `approximate_message_count` obtient le nombre approximatif de messages dans la file d’attente.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Procédure : Suppression d'une file d'attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode `delete_queue_if_exists` sur l’objet file d’attente.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases du Stockage File d’attente, consultez les liens suivants pour en savoir plus sur le Stockage Azure.

- [Utilisation du stockage d'objets blob à partir de C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [Utilisation du stockage de tables à partir de C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Listage des ressources Azure Storage en C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Référence de la bibliothèque de client Stockage Azure pour C++](https://azure.github.io/azure-storage-cpp)
- [Documentation d’Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
