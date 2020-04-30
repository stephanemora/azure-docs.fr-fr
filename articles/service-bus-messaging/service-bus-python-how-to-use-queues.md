---
title: 'Démarrage rapide : Utiliser des files d’attente Azure Service Bus avec Python'
description: Cet article vous explique comment utiliser Python pour créer, envoyer et recevoir des messages à partir des files d'attente Azure Service Bus.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: acb0b0e84804ecf6025e05590133dee9b0d54c48
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80478643"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Démarrage rapide : Utiliser des files d’attente Azure Service Bus avec Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article vous explique comment utiliser Python pour créer, envoyer et recevoir des messages à partir des files d'attente Azure Service Bus. 

Pour plus d’informations sur les bibliothèques Python Azure Service Bus, consultez [Bibliothèques Service Bus pour Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Prérequis
- Un abonnement Azure. Vous pouvez activer les [avantages de votre abonnement Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Un espace de noms Service Bus, créé en suivant les étapes décrites dans [Démarrage rapide : Utilisez le portail Azure pour créer une rubrique Service Bus et des abonnements](service-bus-quickstart-topics-subscriptions-portal.md). Copiez la chaîne de connexion principale à partir de l’écran **Stratégies d’accès partagé** à utiliser plus loin dans cet article. 
- Python 3.4x ou version ultérieure, avec le package [Python Azure Service Bus][Python Azure Service Bus package] installé. Pour plus d’informations, consultez le [Guide d’installation Python](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Créer une file d’attente

Un objet **ServiceBusClient** permet d'utiliser des files d'attente. Pour accéder à Service Bus par programme, ajoutez la ligne suivante vers le début de votre fichier Python :

```python
from azure.servicebus import ServiceBusClient
```

Ajoutez le code suivant pour créer un objet **ServiceBusClient**. Remplacez `<connectionstring>` par la valeur de votre chaîne de connexion principale Service Bus. Vous pouvez trouver cette valeur sous **Stratégies d’accès partagé** dans votre espace de noms Service Bus du [portail Azure][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Le code suivant utilise la méthode `create_queue` de **ServiceBusClient** pour créer une file d'attente nommée `taskqueue` avec les paramètres par défaut :

```python
sb_client.create_queue("taskqueue")
```

Vous pouvez utiliser les options pour remplacer les paramètres de file d'attente par défaut, tels que la durée de vie des messages ou la taille maximale de la rubrique. Le code suivant crée une file d’attente nommée `taskqueue` avec une taille de file d’attente maximale de 5 Go et une valeur TTL d'une minute :

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Envoi de messages à une file d'attente

Pour envoyer un message à une file d’attente Service Bus, une application appelle la méthode `send` sur l’objet **ServiceBusClient**. L’exemple de code suivant crée un client de file d’attente et envoie un message de test à la file d’attente `taskqueue`. Remplacez `<connectionstring>` par la valeur de votre chaîne de connexion principale Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limites et quotas de taille des messages

Les files d’attente Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une file d'attente n’est pas limitée par le nombre de messages qu’elle peut contenir, elle l’est en revanche par la taille totale des messages qu’elle contient. Vous pouvez définir la taille de la file d'attente au moment de la création. La limite maximale est de 5 Go. 

Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Réception des messages d'une file d'attente

Le client de file d’attente reçoit les messages d’une file d’attente à l’aide de la méthode `get_receiver` sur l’objet **ServiceBusClient**. L’exemple de code suivant crée un client de file d’attente et reçoit un message de la file d’attente `taskqueue`. Remplacez `<connectionstring>` par la valeur de votre chaîne de connexion principale Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Utiliser le paramètre peek_lock

Le paramètre facultatif `peek_lock` de `get_receiver` détermine si Service Bus supprime les messages de la file d'attente au fur et à mesure de leur lecture. Le mode par défaut pour la réception des messages est *PeekLock* ou `peek_lock` défini sur **True**, qui lit et verrouille les messages sans les supprimer de la file d'attente. Chaque message doit ensuite être explicitement complété pour être supprimé de la file d'attente.

Pour supprimer des messages de la file d’attente au fur et à mesure de leur lecture, vous pouvez définir le paramètre `peek_lock` de `get_receiver` sur **False**. La suppression de messages dans le cadre de l’opération de réception correspond au modèle le plus simple, mais fonctionne uniquement si l’application peut tolérer les messages manquants en cas de défaillance. Pour mieux comprendre ce comportement, imaginez un scénario dans lequel le consommateur émet une demande de réception et se heurte à un incident avant de la traiter. Si le message a été supprimé lors de la réception, lorsque l’application redémarre et recommence à consommer des messages, elle a manqué le message qu’elle a reçu avant l’incident.

Si votre application ne peut pas tolérer les messages manqués, la réception relève d'une opération en deux étapes. PeekLock recherche le message suivant à consommer, le verrouille pour veiller à ce que d'autres consommateurs ne le reçoivent pas, puis le renvoie à l'application. Une fois le message traité ou stocké, l’application effectue la deuxième étape du processus de réception en appelant la méthode `complete` sur l’objet **Message**.  La méthode `complete` marque le message comme étant utilisé et le supprime de la file d’attente.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message, pour une raison quelconque, elle appelle la méthode `unlock` pour l’objet **Message**. Cela amène Service Bus à déverrouiller le message dans la file d'attente et à permettre sa réception, pour la même application consommatrice ou pour une autre.

Il existe également un délai d’expiration pour les messages verrouillés dans la file d'attente. Si une application ne parvient pas à traiter un message avant l’expiration du verrouillage, par exemple, si l’application se heurte à un incident, Service Bus déverrouille le message automatiquement et permet sa réception.

Si l’application se heurte à un incident après le traitement d’un message, mais avant l’appel de la méthode `complete`, le message est à nouveau remis à l’application lorsqu’elle redémarre. Ce comportement est souvent appelé *Traitement une fois au minimum*. Chaque message est traité au moins une fois, mais dans certaines situations, le même message peut être redistribué. Si votre scénario ne peut pas tolérer un traitement en double, vous pouvez utiliser la propriété **MessageId** du message, qui reste constante entre les tentatives de remise, pour gérer la remise des messages en double. 

> [!TIP]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer vous permet de vous connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction importer/exporter ou la possibilité de tester des rubriques, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les principes de base des files d’attente Service Bus, consultez [Files d’attente, rubriques et abonnements][Queues, topics, and subscriptions] pour plus d’informations.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
