---
title: Guide du développeur Azure Service Bus JMS 2.0
description: Utiliser l’API Java Message Service (JMS) 2.0 pour communiquer avec Azure Service Bus
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726952"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Guide du développeur Azure Service Bus JMS 2.0

Ce guide contient des informations détaillées pour vous aider à bien communiquer avec Azure Service Bus à l’aide de l’API Java Message Service (JMS) 2.0.

En tant que développeur Java, si vous débutez avec Azure Service Bus, nous vous recommandons de consulter les articles ci-dessous.

| Prise en main | Concepts |
|----------------|-------|
| <ul> <li> [Présentation d’Azure Service Bus](service-bus-messaging-overview.md) </li> <li> [Files d’attente, rubriques et abonnements](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Niveau Premium d’Azure Service Bus](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Modèle de programmation JMS (Java Message Service)

Le modèle de programmation de l’API Java Message Service est illustré ci-dessous : 

> [!NOTE]
>
>Le **niveau Premium d’Azure Service Bus** prend en charge JMS 1.1 et JMS 2.0.
> <br> <br>
> **Azure Service Bus niveau Standard** prend en charge des fonctionnalités limitées de JMS 1.1. Pour plus d’informations, consultez cette [documentation](service-bus-java-how-to-use-jms-api-amqp.md).
>

# <a name="jms-20-programming-model"></a>[Modèle de programmation JMS 2.0](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="Diagramme montrant le modèle de programmation JMS 2.0." border="false":::

# <a name="jms-11-programming-model"></a>[Modèle de programmation JMS 1.1](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="Diagramme montrant le modèle de programmation JMS 1.1." border="false":::

---

## <a name="jms---building-blocks"></a>JMS : blocs de construction

Les blocs de construction ci-dessous sont disponibles pour communiquer avec l’application JMS.

> [!NOTE]
> Le guide ci-dessous est basé sur le [Tutoriel Oracle Java EE 6 pour Java Message Service (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> Nous vous recommandons de vous référer à ce tutoriel pour mieux comprendre le service JMS (Java Message Service).
>

### <a name="connection-factory"></a>Fabrique de connexion
L’objet de fabrique de connexion est utilisé par le client pour se connecter au fournisseur JMS. La fabrique de connexion encapsule un ensemble de paramètres de configuration de la connexion définis par l’administrateur.

Chaque fabrique de connexion est une instance de l’interface `ConnectionFactory`, `QueueConnectionFactory` ou `TopicConnectionFactory`.

Pour simplifier la connexion avec Azure Service Bus, ces interfaces sont implémentées respectivement via `ServiceBusJmsConnectionFactory`, `ServiceBusJmsQueueConnectionFactory` et `ServiceBusJmsTopicConnectionFactory`. La fabrique de connexion peut être instanciée avec les paramètres ci-dessous : 
   * Chaîne de connexion : chaîne de connexion pour l’espace de noms du niveau Premium d’Azure Service Bus.
   * Jeu de propriétés ServiceBusJmsConnectionFactorySettings comprenant
      * connectionIdleTimeoutMS : délai d’expiration de connexion inactive en millisecondes.
      * traceFrames : indicateur booléen permettant de collecter des frames de trace de protocole AMQP pour le débogage.
      * *Autres paramètres de configuration*

La fabrique peut être créée comme indiqué ci-dessous. La chaîne de connexion est un paramètre obligatoire, mais les propriétés supplémentaires sont facultatives.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> Les applications Java tirant parti de l’API JMS 2.0 doivent se connecter à Azure Service Bus à l’aide de la chaîne de connexion uniquement. Actuellement, l’authentification pour les clients JMS est uniquement prise en charge à l’aide de la chaîne de connexion.
>
> L’authentification basée sur Azure Active Directory (AAD) n’est pas prise en charge actuellement.
>

### <a name="jms-destination"></a>Destination JMS

Une destination est l’objet qu’un client utilise pour spécifier la cible des messages qu’il génère et la source des messages qu’il consomme.

Les destinations sont mappées vers les entités dans les files d’attente (dans les scénarios point à point) et les rubriques (dans les scénarios publication-abonnement) d’Azure Service Bus.

### <a name="connections"></a>Connexions

Une connexion encapsule une connexion virtuelle avec un fournisseur JMS. Avec Azure Service Bus, cela représente une connexion avec état entre l’application et Azure Service Bus via AMQP.

Une connexion est créée à partir de la fabrique de connexion comme indiqué ci-dessous.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Sessions

Une session est un contexte à thread unique pour la production et la consommation de messages. La session peut être utilisée pour créer des messages, des producteurs et des consommateurs de messages, mais elle fournit également un contexte transactionnel pour permettre le regroupement d’envois et les réceptions dans une unité de travail atomique.

Une session peut être créée à partir de l’objet de connexion comme indiqué ci-dessous.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Modes de session

Une session peut être créée avec l’un des modes ci-dessous.

| Modes de session | Comportement |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | Cette session accuse automatiquement réception d’un message par un client quand la session a été retournée avec succès à partir d’un appel pour recevoir ou quand l’écouteur de message que la session a appelé pour traiter le message est retourné avec succès.|
|**Session.CLIENT_ACKNOWLEDGE** |Le client accuse réception d’un message consommé en appelant la méthode d’accusé de réception du message.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Ce mode d’accusé de réception demande à la session d’accuser réception de la remise des messages en différé.| 
|**Session.SESSION_TRANSACTED**|Cette valeur peut être passée en tant qu’argument à la méthode createSession(int sessionMode) sur l’objet de connexion pour spécifier que la session doit utiliser une transaction locale.| 

Quand le mode de session n’est pas spécifié, **Session.AUTO_ACKNOWLEDGE** est la valeur sélectionnée par défaut.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext est défini dans le cadre de la spécification JMS 2.0.
>

JMSContext combine les fonctionnalités fournies par l’objet de connexion et de session. Il peut être créé à partir de l’objet de fabrique de connexion.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>Modes JMSContext

Tout comme l’objet **Session**, JMSContext peut être créé avec les mêmes modes d’accusation de réception que ceux indiqués dans les [Modes de session](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Quand le mode de session n’est pas spécifié, **JMSContext.AUTO_ACKNOWLEDGE** est la valeur sélectionnée par défaut.

### <a name="jms-message-producers"></a>Producteurs de messages JMS

Un producteur de messages est un objet créé à l’aide de JMSContext ou d’une session et utilisé pour envoyer des messages à une destination.

Il peut être créé en tant qu’objet autonome comme indiqué ci-dessous : 

```java
JMSProducer producer = context.createProducer();
```

ou créé au moment de l’exécution quand un message doit être envoyé.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>Consommateurs de messages JMS

Un consommateur de messages est un objet créé par JMSContext ou une session et utilisé pour recevoir des messages envoyés à une destination. Il peut être créé comme indiqué ci-dessous :

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Réceptions synchrones via la méthode receive()

Le consommateur de messages fournit un moyen synchrone de recevoir des messages à partir de la destination via la méthode `receive()`.

Si aucun argument/délai d’expiration n’est spécifié ou si un délai d’attente de « 0 » est spécifié, le consommateur se bloque indéfiniment sauf si le message arrive ou si la connexion est interrompue (selon ce qui se produit en premier).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Quand un argument positif différent de zéro est fourni, le consommateur se bloque jusqu’à ce que le retardateur expire.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Réceptions asynchrones avec les écouteurs de messages JMS

Un écouteur de messages est un objet utilisé pour la gestion asynchrone des messages sur une destination. Il implémente l’interface `MessageListener` qui contient la méthode `onMessage` dans laquelle la logique métier spécifique doit se trouver.

Un objet écouteur de message doit être instancié et inscrit auprès d’un consommateur de messages spécifique à l’aide de la méthode `setMessageListener`.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Utilisation à partir de rubriques

Les [consommateurs de messages JMS](#jms-message-consumers) sont créés par rapport à une [destination](#jms-destination) qui peut être une file d’attente ou une rubrique.

Les consommateurs sur les files d’attente sont simplement des objets côté client qui se trouvent dans le contexte de la session (et de la connexion) entre l’application cliente et Azure Service Bus.

En revanche, les consommateurs sur les rubriques ont 2 parties : 
   * Un **objet côté client** qui réside dans le contexte de la session (ou JMSContext) et
   * Un **abonnement** qui est une entité sur Azure Service Bus.

Les abonnements sont documentés [ici](java-message-service-20-entities.md#java-message-service-jms-subscriptions) et peuvent être l’un des suivants : 
   * Abonnements durables partagés
   * Abonnements non durables partagés
   * Abonnements durables non partagés
   * Abonnements non durables non partagés

### <a name="jms-queue-browsers"></a>Explorateurs de files d’attente JMS

L’API JMS fournit un objet `QueueBrowser` qui permet à l’application de parcourir les messages de la file d’attente et d’afficher les valeurs d’en-tête pour chaque message.

Un explorateur de file d’attente peut être créé à l’aide de JMSContext comme ci-dessous.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> L’API JMS ne fournit pas d’API permettant de parcourir une rubrique.
>
> Cela est dû au fait que la rubrique elle-même ne stocke pas les messages. Dès que le message est envoyé à la rubrique, il est transféré aux abonnements appropriés.
>

### <a name="jms-message-selectors"></a>Sélecteurs de messages JMS

Les sélecteurs de messages peuvent être utilisés par les applications réceptrices pour filtrer les messages reçus. Avec les sélecteurs de message, l’application de réception décharge la tâche de filtrage des messages au fournisseur JMS (dans ce cas, Azure Service Bus) au lieu de s’en charger elle-même.

Les sélecteurs peuvent être utilisés lors de la création de l’un des consommateurs ci-dessous : 
   * Abonnement durable partagé
   * Abonnement durable non partagé
   * Abonnement non durable partagé
   * Abonnement non durable non partagé
   * Explorateur de files d’attente


## <a name="summary"></a>Résumé

Ce guide du développeur vous a présenté les différentes connexions possibles entre Azure Service Bus et les applications clientes Java utilisant Java Message Service (JMS).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’informations sur Azure Service Bus et de détails sur les entités Java Message Service (JMS), suivez les liens ci-dessous : 
* [Service Bus - Files d’attente, rubriques et abonnements](service-bus-queues-topics-subscriptions.md)
* [Service Bus - entités Java Message Service](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Prise en charge d’AMQP 1.0 dans Service Bus](service-bus-amqp-overview.md)
* [Guide du développeur sur l’utilisation de Service Bus avec AMQP 1.0](service-bus-amqp-dotnet.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [API Java Message Service (doc Oracle externe)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Découvrez comment migrer d’ActiveMQ vers Service Bus](migrate-jms-activemq-to-servicebus.md)
