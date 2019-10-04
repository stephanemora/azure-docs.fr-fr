---
title: Utilisation des files d’attente Azure Service Bus avec Python | Microsoft Docs
description: Découvrez comment utiliser les files d'attente Service Bus Azure depuis Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 9bb53a8e68866e2ed346277171e2706f5907e8af
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141913"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Utilisation des files d’attente Service Bus avec Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Dans ce tutoriel, vous allez apprendre à créer des applications Python afin d’envoyer des messages à une file d’attente Service Bus. 

## <a name="prerequisites"></a>Prérequis
1. Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez [activer les avantages de votre abonnement MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou [vous inscrire pour un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Suivez les étapes de l’article [Utiliser le portail Azure pour créer une file d’attente Service Bus](service-bus-quickstart-portal.md).
    1. Consultez la **vue d’ensemble** rapide des **files d’attente Service Bus**. 
    2. Créez un **espace de noms** Service Bus. 
    3. Obtenez la **chaîne de connexion**. 

        > [!NOTE]
        > Dans ce tutoriel, vous allez créer un **file d’attente** dans l’espace de noms Service Bus à l’aide de Python. 
1. Installez Python ou le [package Python Azure Service Bus][Python Azure Service Bus package], consultez le [Python Installation Guide](/azure/python/python-sdk-azure-install) (Guide d’installation de Python). Consultez la documentation complète du kit SDK de Service Bus Python [ici](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Créer une file d’attente
L’objet **ServiceBusClient** permet d’utiliser des files d’attente. Ajoutez le code suivant au début de chaque fichier Python dans lequel vous souhaitez accéder à Service Bus par programme :

```python
from azure.servicebus import ServiceBusClient
```

Le code suivant crée un objet **ServiceBusClient**. Remplacez `<CONNECTION STRING>` par votre connectionstring servicebus.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Le nom et la valeur de la clé de signature d’accès partagé se trouvent dans les informations de connexion du [Portail Microsoft Azure][Azure portal] ou dans le volet **Propriétés** de Visual Studio quand vous sélectionnez l’espace de noms Service Bus dans l’Explorateur de serveurs (comme indiqué dans la section précédente).

```python
sb_client.create_queue("taskqueue")
```

La méthode `create_queue` prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de file d’attente par défaut comme la durée de vie (TTL) du message ou la taille maximale de la file d’attente. L’exemple suivant définit la taille maximale de la file d’attente sur 5 Go et la durée de vie de message sur 1 minute :

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Pour plus d’informations, consultez la [documentation relative à Azure Service Bus Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Envoi de messages à une file d'attente
Pour envoyer un message à une file d’attente Service Bus, votre application appelle la méthode `send` sur l’objet `ServiceBusClient`.

L’exemple suivant indique comment envoyer un message test à la file d’attente nommée `taskqueue` au moyen de la méthode `send_queue_message` :

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][Service Bus quotas].

Pour plus d’informations, consultez la [documentation relative à Azure Service Bus Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Réception des messages d'une file d'attente
La méthode `get_receiver` de l’objet `ServiceBusService` permet de recevoir les messages d’une file d’attente :

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Pour plus d’informations, consultez la [documentation relative à Azure Service Bus Python](/python/api/overview/azure/servicebus?view=azure-python).


Les messages sont supprimés de la file d’attente au fur et à mesure de leur lecture, si le paramètre `peek_lock` est défini sur **False**. Vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre `peek_lock` sur **True**.

Le comportement de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

Si le paramètre `peek_lock` est défini sur **True**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer de messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete** sur l’objet **Message**. La méthode **delete** marque le message comme étant consommé et le supprime de la file d’attente.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d’application et des messages illisibles
Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock** pour l’objet **Message**. Service Bus déverrouille alors le message dans la file d’attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’appel de la méthode **delete**, le message est à nouveau remis à l’application lorsqu’elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Pour ce faire, il suffit souvent d’utiliser la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

> [!NOTE]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer permet aux utilisateurs de se connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction d’importation/exportation ou la possibilité de tester une rubrique, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus.

* [Files d’attente, rubriques et abonnements.][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

