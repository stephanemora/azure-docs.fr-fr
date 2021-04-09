---
title: Messagerie Azure Service Bus – Entités Java Message Service
description: Cet article fournit une vue d’ensemble des entités de messagerie Azure Service Bus via l’API de service de messagerie Java.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652579"
---
# <a name="java-message-service-jms-20-entities"></a>Entités Java message service (JMS) 2.0

Les applications clientes se connectant à Azure Service Bus Premium et utilisant la [bibliothèque JMS Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) peuvent se servir des entités ci-dessous.

## <a name="queues"></a>Files d’attente

Les files d’attente dans JMS sont sémantiquement comparables aux [files d’attente Service Bus](service-bus-queues-topics-subscriptions.md#queues) traditionnelles.

Pour créer une file d’attente, utilisez les méthodes ci-dessous dans la classe `JMSContext` :

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Rubriques

Les rubriques dans JMS sont sémantiquement comparables aux [files d’attente Service Bus](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) traditionnelles.

Pour créer une rubrique, utilisez les méthodes ci-dessous dans la classe `JMSContext` :

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Files d’attente temporaires

Si une application cliente nécessite une entité temporaire qui existe pendant la durée de vie de l’application, elle peut utiliser des files d’attente temporaires. Ces entités sont utilisées dans le modèle [Demande-Réponse](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html).

Pour créer une file d’attente temporaire, utilisez les méthodes ci-dessous dans la classe `JMSContext` :

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Rubriques temporaires

Tout comme les files d’attente temporaires, des rubriques temporaires permettent la publication/l’abonnement via une entité temporaire qui existe pendant la durée de vie de l’application.

Pour créer une rubrique temporaire, utilisez les méthodes ci-dessous dans la classe `JMSContext` :

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JMS (Java Message Service) et abonnements

Bien qu’elles soient sémantiquement similaires aux [Abonnements](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (qui, en fait, existent sur une rubrique et activent la sémantique de publication/abonnement), les spécifications de Java Message Service présentent les concepts d’attributs **Partagé**, **Non partagé**, **Durable et **Non durable** pour un abonnement donné.

> [!NOTE]
> Les abonnements ci-dessous sont disponibles dans le niveau Azure Service Bus Premium pour les applications clientes se connectant à Azure Service Bus à l’aide de la [bibliothèque JMS Azure Service Bus](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Seuls les abonnements durables peuvent être créés avec le portail Azure.
>

### <a name="shared-durable-subscriptions"></a>Abonnements durables partagés

Un abonnement durable partagé est utilisé lorsque tous les messages publiés sur une rubrique doivent être reçus et traités par une application, que l’application consomme ou non activement l’abonnement à tout moment.

Toute application authentifiée pour recevoir des messages de Service Bus peut en recevoir de l’abonnement durable partagé.

Pour créer un abonnement durable partagé, utilisez les méthodes ci-dessous sur la classe `JMSContext` –

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

L’abonnement durable partagé continue à exister, sauf s’il est supprimé à l’aide de la méthode `unsubscribe` sur la classe `JMSContext`.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Abonnements durables non partagés

À l’instar d’un abonnement durable partagé, un abonnement durable non partagé est utilisé lorsque tous les messages publiés sur une rubrique doivent être reçus et traités par une application, que l’application consomme ou non activement l’abonnement.

Toutefois, étant donné qu’il s’agit d’un abonnement non partagé, seule l’application qui a créé l’abonnement peut recevoir des messages de ce dernier.

Pour créer un abonnement durable non partagé, utilisez les méthodes ci-dessous à partir de la classe `JMSContext` – 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> La fonctionnalité `noLocal` n’est actuellement pas prise en charge et est ignorée.
>

L’abonnement durable non partagé continue à exister, sauf s’il est supprimé à l’aide de la méthode `unsubscribe` sur la classe `JMSContext`.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Abonnements non durables partagés

Un abonnement partagé non durable est utilisé lorsque plusieurs applications clientes doivent recevoir et traiter des messages d’un seul abonnement, uniquement jusqu’à ce qu’ils les consomment/les reçoivent activement.

Étant donné que l’abonnement n’est pas durable, il n’est pas persistant. Les messages ne sont pas reçus par cet abonnement lorsqu’il n’y a aucun consommateur actif.

Pour créer un abonnement partagé non durable, créez un `JmsConsumer` comme indiqué dans les méthodes ci-dessous de la classe `JMSContext` –

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

L’abonnement partagé non durable continue d’exister jusqu’à ce qu’il y ait des consommateurs actifs qui le reçoivent.

### <a name="unshared-non-durable-subscriptions"></a>Abonnements non durables non partagés

Un abonnement non partagé non durable est utilisé lorsque l’application cliente doit recevoir et traiter des messages d’un seul abonnement, uniquement jusqu’à ce qu’elle les consomme/les reçoive activement. Un seul consommateur peut exister sur cet abonnement, autrement dit le client qui a créé l’abonnement.

Étant donné que l’abonnement n’est pas durable, il n’est pas persistant. Les messages ne sont pas reçus par cet abonnement lorsqu’il n’y a aucun consommateur actif.

Pour créer un abonnement non-partagé non durable, créez un `JMSConsumer` comme indiqué dans les méthodes ci-dessous de la classe `JMSContext` –

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> La fonctionnalité `noLocal` n’est actuellement pas prise en charge et est ignorée.
>

L’abonnement non partagé non durable continue d’exister jusqu’à ce qu’il y ait un consommateur actif qui le reçoit.

### <a name="message-selectors"></a>Sélecteurs de messages

À l’instar des **filtres et actions** qui existent pour les abonnements Service Bus standard, les **sélecteurs de messages** existent pour les abonnements JMS.

Les sélecteurs de messages peuvent être configurés sur chacun des abonnements JMS et exister sous la forme d’une condition de filtre sur les propriétés d’en-tête de message. Seuls les messages avec des propriétés d’en-tête correspondant à l’expression du sélecteur de messages sont remis. La valeur Null ou une chaîne vide indique qu’il n’existe aucun sélecteur de message pour l’abonnement/le consommateur JMS.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Concepts supplémentaires pour les abonnements Java Message Service (JMS) 2.0

### <a name="client-scoping"></a>Étendue du client

Les abonnements, tels qu’ils sont spécifiés dans l’API Java Message Service (JMS) 2.0, peuvent être ou non *étendus à une ou à des applications clientes spécifiques* (identifiées avec l’élément `clientId` approprié).

Une fois l’abonnement délimité, il **n’est accessible** qu’à partir d’applications clientes qui ont le même ID client. 

Toute tentative d’accès à un abonnement délimité sur un ID client particulier (par exemple, clientId1) depuis une application ayant un autre ID client (par exemple, clientId2) entraîne la création d’un autre abonnement délimité sur l’autre ID client (clientId2).

> [!NOTE]
> L’ID client peut prendre la valeur null ou être vide, mais il doit correspondre à l’ID client défini sur l’application cliente JMS. Pour Azure Service Bus, un ID client null et un ID client vide ont le même comportement.
>
> Si l’ID client est défini sur la valeur null ou qu’il est vide, il n’est accessible qu’aux applications clientes dont l’ID client est également défini sur null ou est vide.
>

### <a name="shareability"></a>Partageabilité

Les abonnements **partagés** permettent à plusieurs clients/consommateurs (autrement dit, des objets JMSConsumer) de recevoir les messages qu’ils envoient.

>[!NOTE]
> Les abonnements partagés dont l’étendue correspond à un ID client spécifique sont toujours accessibles par plusieurs clients/consommateurs (c’est-à-dire des objets JMSConsumer), mais chacune des applications clientes doit avoir le même ID client.
>
 

Les abonnements non **partagés** n’autorisent qu’un seul client/consommateur (c’est-à-dire l’objet JMSConsumer) à recevoir les messages qu’ils envoient. Si un objet `JMSConsumer` est créé sur un abonnement non partagé alors qu’il a déjà une écoute `JMSConsumer` active des messages qu’il contient, une exception `JMSException` est déclenchée.


### <a name="durability"></a>Durabilité

Les abonnements **durables** sont conservés et continuent à collecter des messages à partir de la rubrique, qu’une application (`JMSConsumer`) consomme ou non des messages à partir de celle-ci.

Les abonnements **non durables** ne sont pas rendus persistants et recueillent des messages à partir de la rubrique tant qu’une application (`JMSConsumer`) utilise des messages de celle-ci. 

## <a name="representation-of-client-scoped-subscriptions"></a>Représentation d’abonnements étendus aux clients

Étant donné que les abonnements (JMS) étendus aux clients doivent coexister avec les [abonnements](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) existants, la façon dont les abonnements étendus aux clients (JMS) sont représentés suit le format ci-dessous.

   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**D** (pour les abonnements durables)
   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**ND** (pour les abonnements non durables)

Ici, **$** est le délimiteur.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations et des exemples d’utilisation de la messagerie Service Bus, consultez les rubriques avancées suivantes :

* [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
* [Utiliser Java Message Service 2.0 avec Azure Service Bus Premium](how-to-use-java-message-service-20.md)



