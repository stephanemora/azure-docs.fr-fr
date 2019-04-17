---
title: Utilisation des rubriques Azure Service Bus avec Python | Microsoft Docs
description: Découvrez comment utiliser les rubriques et abonnements Service Bus Azure depuis Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 102fe85916194648501be3d2cb39d8bcda9e9f5c
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607073"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Utilisation des rubriques et abonnements Service Bus avec Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article explique comment utiliser les rubriques et les abonnements Service Bus. Ces exemples sont écrits en Python et utilisent le [package du Kit de développement logiciel (SDK) Azure Python][Azure Python package]. Parmi les scénarios présentés :

- Création de rubriques et d’abonnements 
- Création de filtres d’abonnement 
- Envoi de messages à une rubrique 
- Réception de messages à partir d’un abonnement
- Suppression de rubriques et d’abonnements

## <a name="prerequisites"></a>Conditions préalables
1. Un abonnement Azure. Pour suivre ce tutoriel, vous avez besoin d’un compte Azure. Vous pouvez activer votre [avantages pour les abonnés Visual Studio ou MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Suivez les étapes de la [Guide de démarrage rapide : Utiliser le portail Azure pour créer une rubrique Service Bus et des abonnements à la rubrique](service-bus-quickstart-topics-subscriptions-portal.md) pour créer un Service Bus **espace de noms** et obtenir le **chaîne de connexion**.

    > [!NOTE]
    > Vous allez créer un **rubrique** et un **abonnement** à la rubrique à l’aide de **Python** dans ce démarrage rapide. 
3. Installer [package Azure Python][Azure Python package]. Consultez le [Guide d’Installation de Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Création d'une rubrique

L’objet **ServiceBusService** permet d’utiliser des rubriques. Ajoutez le code suivant au début de chaque fichier Python dans lequel vous souhaitez accéder à Service Bus par programme :

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Le code suivant crée un objet **ServiceBusService**. Remplacez `mynamespace`, `sharedaccesskeyname` et `sharedaccesskey` par un espace de noms, un nom et une valeur de clé de signature d’accès partagé (SAP) réels.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Vous pouvez obtenir les valeurs pour la valeur et le nom de la clé SAP à partir du [portail Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

La méthode `create_topic` prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de rubrique par défaut, comme la durée de vie du message ou la taille maximale de la rubrique. L’exemple suivant définit la taille maximale de la rubrique sur 5 Go et la durée de vie de message (TTL) sur une minute :

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Création d’abonnements

Les abonnements à des rubriques sont également créés à l’aide de l’objet **ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

> [!NOTE]
> Les abonnements sont persistants et continuent à exister jusqu’à leur suppression ou celle de la rubrique à laquelle ils sont abonnés.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Création d’un abonnement avec le filtre par défaut (MatchAll)

Si aucun filtre n’est spécifié lors de la création d’un abonnement, le filtre **MatchAll** (par défaut) est utilisé. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. L’exemple suivant crée un abonnement intitulé `AllMessages` et utilise le filtre par défaut **MatchAll**.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Création d’abonnements avec des filtres

Vous pouvez également définir des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Le type de filtre le plus flexible pris en charge par les abonnements est **SqlFilter**, qui implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus d’informations sur les expressions utilisables avec un filtre SQL, consultez la syntaxe de [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Vous pouvez ajouter des filtres à un abonnement en utilisant la méthode **create\_rule** de l’objet **ServiceBusService**. Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

> [!NOTE]
> Comme le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez commencer par supprimer le filtre par défaut, sans quoi le filtre **MatchAll** remplace tous les autres filtres spécifiés. Vous pouvez supprimer la règle par défaut en utilisant la méthode `delete_rule` de l’objet **ServiceBusService**.
> 
> 

L’exemple suivant crée l’abonnement `HighMessages` avec un objet **SqlFilter** qui ne sélectionne que les messages dont la propriété personnalisée `messagenumber` a une valeur supérieure à 3 :

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

De même, l’exemple suivant crée l’abonnement `LowMessages` avec un objet **SqlFilter** qui ne sélectionne que les messages dont la propriété `messagenumber` a une valeur inférieure ou égale à 3 :

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

À présent, dès lors qu’un message est envoyé à `mytopic`, il est toujours remis aux destinataires abonnés à l’abonnement de rubrique **AllMessages** et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique **HighMessages** et **LowMessages** (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode `send_topic_message` de l’objet **ServiceBusService**.

L’exemple suivant montre comment envoyer cinq messages de test à `mytopic`. La valeur de la propriété `messagenumber` de chaque message varie au niveau de l’itération de la boucle (ce qui détermine les abonnements qui le reçoivent) :

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Quotas Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement

La méthode `receive_subscription_message` de l’objet **ServiceBusService** permet de recevoir les messages associés à un abonnement.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Les messages sont supprimés de l’abonnement au fur et à mesure de leur lecture si le paramètre `peek_lock` est défini sur **False**. Vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de la file d’attente en définissant le paramètre `peek_lock` sur **True**.

Le comportement de lecture et de suppression du message dans le cadre de l’opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application peut tolérer le non-traitement d’un message après un échec. Pour mieux comprendre ce comportement, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Étant donné que Service Bus a marqué le message comme étant consommé, quand l’application redémarre et recommence à consommer des messages, elle a manqué le message consommé avant l’incident.

Si le paramètre `peek_lock` est défini sur **True**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer de messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode `delete` sur l’objet **Message**. La méthode `delete` marque le message comme étant consommé et le supprime de l’abonnement.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message, pour une raison quelconque, elle appelle la méthode `unlock` pour l’objet **Message**. Cette méthode amène Service Bus à déverrouiller le message dans l’abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans un abonnement est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’appel de la méthode `delete`, le message est à nouveau remis à l’application lorsqu’elle redémarre. Ce traitement est souvent appelé Au moins une fois le traitement\*; autrement dit, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Pour cela, vous pouvez utiliser la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Suppression de rubriques et d'abonnements

Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite par le biais du [portail Azure][Azure portal] ou par programme. L’exemple suivant indique comment supprimer la rubrique nommée `mytopic` :

```python
bus_service.delete_topic('mytopic')
```

La suppression d’une rubrique a également pour effet de supprimer les abonnements inscrits dans la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l’abonnement `HighMessages` de la rubrique `mytopic` :

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus.

* Consultez [Files d’attente, rubriques et abonnements][Queues, topics, and subscriptions].
* Référence pour [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
