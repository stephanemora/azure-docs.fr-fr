---
title: Comment utiliser le stockage Files d’attente Azure v2.1 à partir de Python - Stockage Azure
description: Découvrez comment utiliser le service de File d'attente Azure v2.1 à partir de Python pour créer et supprimer des files d'attente et insérer, obtenir et supprimer des messages.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 97cb41a47b18d403881432098ecdda9bb26146d2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848178"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Comment utiliser le stockage Files d’attente Azure v2.1 à partir de Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Cet article présente des scénarios courants d’utilisation du service Stockage File d’attente Azure. Les scénarios traités incluent l’insertion, la lecture furtive, la récupération et la suppression des messages de file d’attente, ainsi que la création et la suppression de files d’attente.

> [!IMPORTANT]
> Cet article fait référence à la version héritée de la bibliothèque cliente Azure Storage pour Python. Pour vous familiariser avec la dernière version, consultez [Démarrage rapide : Bibliothèque cliente Stockage File d’attente Azure pour Python](storage-quickstart-queues-python.md)

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble

Les exemples de cet article sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Pour plus d’informations sur les files d’attente, consultez la section [Étapes suivantes](#next-steps).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Télécharger et installer le SDK Stockage Azure pour Python

Le [SDK Stockage Azure pour Python](https://github.com/azure/azure-storage-python) nécessite Python versions 2.7, 3.3 et ultérieures.
 
### <a name="install-via-pypi"></a>Effectuer l’installation via PyPi

Pour effectuer l’installation via PyPI (Python Package Index), tapez :

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Si vous mettez à niveau la version 0.36 ou antérieure du SDK Stockage Azure pour Python, désinstallez l’ancien SDK à l’aide de `pip uninstall azure-storage` avant d’installer le dernier package.

Pour connaître les autres méthodes d’installation, consultez [SDK Stockage Azure pour Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Voir l’exemple d’application

Pour voir et exécuter un exemple d’application qui montre comment utiliser Python avec les files d’attente Azure, consultez [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Pour exécuter l’exemple d’application, vous devez avoir installé les packages `azure-storage-queue` et `azure-storage-common`.

## <a name="create-a-queue"></a>Créer une file d’attente

L'objet [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) permet d'utiliser des files d'attente. Le code suivant crée un objet `QueueService`. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

```python
from azure.storage.queue import QueueService
```

Le code suivant crée un objet `QueueService` à l’aide du nom et de la clé du compte de stockage. Remplacez *myaccount* et *mykey* par le nom et la clé de votre compte.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d’attente, utilisez la méthode [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) afin de créer un message et l’ajouter à la file d’attente.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Les messages de la file d’attente Azure sont stockés sous forme de texte. Si vous souhaitez stocker des données binaires, configurez les fonctions d’encodage et de décodage base64 sur l’objet de service de file d’attente avant de placer un message dans la file d’attente.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d’attente sans le supprimer de celle-ci en appelant la méthode [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-). Par défaut, `peek_messages` lit furtivement un seul message.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Enlever des messages d’une file d’attente

Votre code supprime un message d'une file d'attente en deux étapes. Quand vous appelez [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), vous obtenez le message suivant dans une file d’attente par défaut. Un message renvoyé par `get_messages` devient invisible par les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d’attente, vous devez aussi appeler [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Ce processus de suppression d’un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle `delete_message` juste après le traitement du message.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L’exemple de code suivant utilise la méthode `get_messages` pour obtenir 16 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle for. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code ci-dessous utilise la méthode [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) pour mettre à jour un message. Ce délai de visibilité est défini sur 0, ce qui signifie que le message s’affiche immédiatement et que le contenu est mis à jour.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) demande au service de file d’attente de retourner les métadonnées relatives à la file d’attente et le nombre approximatif de messages (`approximate_message_count`). Le résultat est seulement approximatif, car des messages peuvent être ajoutés ou supprimés après que le service de file d'attente a répondu à votre demande.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Suppression d'une file d'attente

Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-).

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage de files d’attente, consultez les liens suivants pour en savoir plus.

* [Informations de référence sur l’API Python Files d’attente Azure](/python/api/azure-storage-queue)
* [Centre de développement Python](https://azure.microsoft.com/develop/python/)
* [API REST des services d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python
