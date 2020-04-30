---
title: 'Démarrage rapide : Utiliser des rubriques et abonnements Azure Service Bus avec Python'
description: Cet article explique comment créer une rubrique Azure Service Bus, créer un abonnement, envoyer des messages à une rubrique et recevoir des messages d’un abonnement.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 4745d675086f1b07bf7fccf17c14c76e4b18fba2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "80478076"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Démarrage rapide : Utiliser des rubriques et des abonnements Service Bus avec Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article explique comment utiliser Python avec des rubriques et des abonnements Azure Service Bus. Les exemples utilisent le package du [kit de développement logiciel (SDK) Azure Python][Azure Python package] pour : 

- créer des rubriques et des abonnements à des rubriques
- créer des filtres et des règles d’abonnement
- envoyer des messages aux rubriques 
- recevoir des messages d’abonnements
- Suppression de rubriques et d'abonnements

## <a name="prerequisites"></a>Prérequis
- Un abonnement Azure. Vous pouvez activer les [avantages de votre abonnement Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Un espace de noms Service Bus, créé en suivant les étapes décrites dans [Démarrage rapide : Utilisez le portail Azure pour créer une rubrique Service Bus et des abonnements](service-bus-quickstart-topics-subscriptions-portal.md). Copiez le nom de l’espace de noms, le nom de la clé d’accès partagé et la valeur de clé primaire à partir de l’écran **Stratégies d’accès partagé** à utiliser ultérieurement dans ce démarrage rapide. 
- Python 3.4x ou version ultérieure, avec le package du [kit de développement logiciel (SDK) Azure Python][Azure Python package] installé. Pour plus d’informations, consultez le [Guide d’installation Python](/azure/developer/python/azure-sdk-install).

## <a name="create-a-servicebusservice-object"></a>Créer un objet ServiceBusService

Un objet **ServiceBusService** vous permet d’utiliser des rubriques et des abonnements à des rubriques. Pour accéder à Service Bus par programme, ajoutez la ligne suivante vers le début de votre fichier Python :

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Ajoutez le code suivant pour créer un objet **ServiceBusService**. Remplacez `<namespace>`, `<sharedaccesskeyname>` et `<sharedaccesskeyvalue>` par le nom de votre espace de noms Service Bus, le nom clé de la signature d’accès partagé (SAP) et la valeur de clé primaire. Vous pouvez trouver ces valeurs sous **Stratégies d’accès partagé** dans votre espace de noms Service Bus du [Portail Azure][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Création d'une rubrique

Le code suivant utilise la méthode `create_topic` pour créer une rubrique Service Bus appelée `mytopic`, avec des paramètres par défaut :

```python
bus_service.create_topic('mytopic')
```

Vous pouvez utiliser les options de rubrique pour remplacer les paramètres de rubrique par défaut, tels que la durée de vie des messages ou la taille maximale de la rubrique. L’exemple suivant crée une rubrique nommée `mytopic` avec une taille de rubrique maximale de 5 Go et une durée de vie de message par défaut d’une minute :

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Création d’abonnements

Vous utilisez également l’objet **ServiceBusService** pour créer des abonnements à des rubriques. Un abonnement peut avoir un filtre pour limiter l’ensemble de messages remis à sa file d’attente virtuelle. Si vous ne spécifiez pas de filtre, les nouveaux abonnements utilisent le filtre par défaut **MatchAll**, ce qui place tous les messages publiés dans la rubrique dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement à `mytopic` intitulé `AllMessages` qui utilise le filtre **MatchAll** :

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Utiliser des filtres avec des abonnements

Utilisez la méthode `create_rule` de l’objet **ServiceBusService** pour filtrer les messages qui s’affichent dans un abonnement. Vous pouvez spécifier des règles lorsque vous créez l’abonnement ou ajouter des règles à des abonnements existants.

Le type de filtre le plus flexible est un **SqlFilter**, qui utilise un sous-ensemble de SQL-92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d’informations sur les expressions utilisables avec un filtre SQL, consultez la syntaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Comme le filtre par défaut **MatchAll** est appliqué automatiquement à tous les nouveaux abonnements, vous devez le supprimer des abonnements à filtrer, sans quoi **MatchAll** remplacera tous les autres filtres spécifiés. Vous pouvez supprimer la règle par défaut en utilisant la méthode `delete_rule` de l’objet **ServiceBusService**.

L’exemple suivant crée un abonnement à `mytopic` nommé `HighMessages`, avec une règle **SqlFilter** nommée `HighMessageFilter`. La règle `HighMessageFilter` sélectionne uniquement les messages dont la propriété personnalisée `messageposition` est supérieure à 3 :

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

L’exemple suivant crée un abonnement à `mytopic` nommé `LowMessages`, avec une règle **SqlFilter** nommée `LowMessageFilter`. La règle `LowMessageFilter` sélectionne uniquement les messages dont la propriété personnalisée `messageposition` est inférieure ou égale à 3 :

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Avec `AllMessages`, `HighMessages` et `LowMessages` activés, les messages envoyés à `mytopic` sont toujours remis aux destinataires de l’abonnement `AllMessages`. Les messages sont également remis de manière sélective à l’abonnement `HighMessages` ou `LowMessages`, en fonction de la valeur de la propriété `messageposition` du message. 

## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique

Les applications utilisent la méthode `send_topic_message` de l’objet **ServiceBusService** pour envoyer des messages à une rubrique Service Bus.

L’exemple suivant envoie cinq messages de test à la rubrique `mytopic`. La valeur de propriété `messageposition` personnalisée dépend de l’itération de la boucle et détermine les abonnements qui reçoivent les messages. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limites et quotas de taille des messages

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une rubrique n’est pas limitée par le nombre de messages qu’elle peut contenir, elle l’est en revanche par la taille totale des messages qu’elle contient. Vous pouvez définir la taille de la rubrique au moment de la création. La limite maximale est de 5 Go. 

Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement

Les applications utilisent la méthode `receive_subscription_message` de l’objet **ServiceBusService** pour recevoir les messages d’un abonnement. L’exemple suivant reçoit les messages de l’abonnement `LowMessages` et les supprime au fur et à mesure de leur lecture :

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Le paramètre facultatif `peek_lock` de `receive_subscription_message` détermine si Service Bus supprime les messages de l’abonnement au fur et à mesure de leur lecture. Le mode par défaut pour la réception des messages est *PeekLock* ou `peek_lock` défini sur **True**, qui lit et verrouille les messages sans les supprimer de l’abonnement. Chaque message doit ensuite être explicitement complété pour être supprimé de l’abonnement.

Pour supprimer des messages de l’abonnement au fur et à mesure de leur lecture, vous pouvez définir le paramètre `peek_lock` sur **False**, comme dans l’exemple précédent. La suppression de messages dans le cadre de l’opération de réception est le modèle le plus simple et fonctionne correctement si l’application peut tolérer les messages manquants en cas de défaillance. Pour mieux comprendre ce comportement, imaginez un scénario dans lequel l’application émet la requête de réception et subit un incident avant de la traiter. Si le message a été supprimé lors de la réception, lorsque l’application redémarre et recommence à consommer des messages, elle a manqué le message qu’elle a reçu avant l’incident.

Si votre application ne peut pas tolérer les messages manqués, la réception devient une opération en deux étapes. PeekLock recherche le message suivant à consommer, le verrouille pour veiller à ce que d'autres consommateurs ne le reçoivent pas, puis le renvoie à l'application. Une fois le message traité ou stocké, l’application effectue la deuxième étape du processus de réception en appelant la méthode `complete` sur l’objet **Message**.  La méthode `complete` marque le message comme étant consommé et le supprime de l’abonnement.

L’exemple suivant illustre un scénario Peek-Lock :

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message, pour une raison quelconque, elle appelle la méthode `unlock` pour l’objet **Message**. Cela amène Service Bus à déverrouiller le message dans l’abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

Il y a également un délai d’expiration pour les messages verrouillés au sein de l’abonnement. Si une application ne parvient pas à traiter un message avant l’expiration du verrouillage, par exemple, si l’application se heurte à un incident, Service Bus déverrouille le message automatiquement et permet sa réception.

Si l’application subit un incident après le traitement d’un message, mais avant l’appel de la méthode `complete`, le message est à nouveau remis à l’application lorsqu’elle redémarre. Ce comportement est souvent appelé *Traitement une fois au minimum*. Chaque message est traité au moins une fois, mais dans certaines situations, le même message peut être redistribué. Si votre scénario ne peut pas tolérer un traitement en double, vous pouvez utiliser la propriété **MessageId** du message, qui reste constante entre les tentatives de remise, pour gérer la remise des messages en double. 

## <a name="delete-topics-and-subscriptions"></a>Suppression de rubriques et d'abonnements

Pour supprimer des rubriques et des abonnements, utilisez la méthode [Portail Azure][Azure portal] ou `delete_topic`. Le code suivant supprime la rubrique nommée `mytopic` :

```python
bus_service.delete_topic('mytopic')
```

La suppression d’une rubrique supprime tous les abonnements à la rubrique. Vous pouvez également supprimer des abonnements de manière indépendante. Le code suivant montre comment supprimer l’abonnement nommé`HighMessages` de la rubrique `mytopic` :

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Par défaut, les rubriques et les abonnements sont persistants et existent jusqu’à ce que vous les supprimiez. Pour supprimer automatiquement des abonnements après l’expiration d’une période donnée, vous pouvez définir le paramètre [auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) sur l’abonnement. 

> [!TIP]
> Vous pouvez gérer les ressources Service Bus à l'aide de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer vous permet de vous connecter à un espace de noms Service Bus et de gérer les entités de messagerie en toute simplicité. L’outil fournit des fonctionnalités avancées telles que la fonction importer/exporter ou la possibilité de tester des rubriques, des files d’attente, des abonnements, des services de relais, des hubs de notification et des hubs d’événements. 

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus :

* [Files d’attente, rubriques et abonnements.][Queues, topics, and subscriptions]
* Référence à [SqlFilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
