---
title: Utilisation du stockage de files d’attente à partir de Python | Microsoft Docs
description: Découvrez comment utiliser le service de File d’attente Azure à partir de Python pour créer et supprimer des files d’attente, ainsi que pour insérer, récupérer et supprimer des messages.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: tamram
ms.component: queues
ms.openlocfilehash: 0edb90ca7324d47beaa5133d423928e615ff33a9
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742811"
---
# <a name="how-to-use-queue-storage-from-python"></a>Utilisation du stockage de files d'attente à partir de Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Vue d’ensemble
Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de files d'attente Azure. Les exemples sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Les scénarios traités incluent **l’insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d’attente, ainsi que la **création et suppression des files d’attente**. Pour plus d’informations sur les files d’attente, consultez la section [Étapes suivantes].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Télécharger et installer le SDK Stockage Azure pour Python

Le [SDK Stockage Azure pour Python](https://github.com/azure/azure-storage-python) nécessite Python 2.7, 3.3, 3.4, 3.5 ou 3.6.
 
### <a name="install-via-pypi"></a>Effectuer l’installation via PyPi

Pour effectuer l’installation via PyPI (Python Package Index), tapez :

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Si vous mettez à niveau la version 0.36 ou antérieure du SDK Stockage Azure pour Python, désinstallez l’ancien SDK à l’aide de `pip uninstall azure-storage` avant d’installer le dernier package.

Pour connaître les autres méthodes d’installation, consultez [SDK Stockage Azure pour Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Voir l’exemple d’application

Pour voir et exécuter un exemple d’application qui montre comment utiliser Python avec les files d’attente Azure, consultez [Azure Storage: Getting Started with Azure Queues in Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Pour exécuter l’exemple d’application, vous devez avoir installé les packages `azure-storage-queue` et `azure-storage-common`.

## <a name="how-to-create-a-queue"></a>Procédure : Création d’une file d’attente

L'objet **QueueService** permet d'utiliser des files d'attente. Le code suivant permet de créer un objet **QueueService** . Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

```python
from azure.storage.queue import QueueService
```

Le code suivant permet de créer un objet **QueueService** en utilisant le nom et la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le nom et la clé réels de votre compte.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procédure : Insertion d’un message dans une file d’attente
Pour insérer un message dans une file d’attente, utilisez la méthode **put\_message** pour créer un nouveau message et l’ajouter à la file d’attente.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Procédure : Lecture furtive du message suivant
Vous pouvez lire furtivement le message au début de la file d’attente sans l’enlever de la file d’attente en appelant la méthode **peek\_messages**. Par défaut, **peek\_messages** lit furtivement un seul message.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Procédure : Retrait des messages de la file d’attente
Votre code supprime un message d'une file d'attente en deux étapes. Lorsque vous appelez **get\_messages**, vous obtenez le message suivant dans une file d’attente par défaut. Un message renvoyé par **get\_messages** devient invisible par les autres codes lisant les messages de cette file d’attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d’attente, vous devez aussi appeler **delete\_message**. Ce processus de suppression d’un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete\_message** juste après le traitement du message.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Il existe deux façons de personnaliser la récupération des messages à partir d'une file d'attente.
Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L’exemple de code suivant utilise la méthode **get\_messages** pour obtenir 16 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle for. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procédure : Modification du contenu d’un message en file d’attente
Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code ci-dessous utilise la méthode **update\_message** pour mettre à jour un message. Ce délai de visibilité est défini sur 0, ce qui signifie que le message s’affiche immédiatement et que le contenu est mis à jour.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Procédure : Obtention de la longueur de la file d’attente
Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **get\_queue\_metadata** demande au service de File d’attente de renvoyer les métadonnées relatives à la file d’attente, et la valeur **approximate_message_count**. Le résultat est seulement approximatif, car des messages peuvent être ajoutés ou supprimés après que le service de file d'attente a répondu à votre demande.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Procédure : Suppression d’une file d’attente
Pour supprimer une file d’attente et tous les messages qu’elle contient, appelez la méthode **delete\_queue**.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les bases du stockage de files d’attente, consultez les liens suivants pour en savoir plus.

* [Centre de développement Python](https://azure.microsoft.com/develop/python/)
* [API REST des services d’Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python
