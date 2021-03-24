---
title: Utiliser Java Message Service 2.0 avec Azure Service Bus Premium
description: Utiliser Java Message Service (JMS) avec Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 894821444f74248b73578595df943cb3a0025360
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698397"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Utiliser Java Message Service 2.0 avec Azure Service Bus Premium

Cet article explique comment utiliser l’API populaire **Java Message Service (JMS) 2.0** pour interagir avec Azure Service Bus via le protocole AMQP 1.0 (Advanced Message Queueing Protocol).

> [!NOTE]
> La prise en charge de l’API 2.0 de Java Message Service (JMS) est disponible uniquement sur le **niveau Premium d’Azure Service Bus**.
>

## <a name="pre-requisites"></a>Conditions préalables

### <a name="get-started-with-service-bus"></a>Prise en main de Service Bus

Ce guide présuppose que vous disposez déjà d’un espace de noms Service Bus. Dans le cas contraire, vous pouvez [créer l’espace de noms et la file d’attente](service-bus-create-namespace-portal.md) à l’aide du [Portail Azure](https://portal.azure.com). 

Pour plus d’informations sur la création d’espaces de noms et de files d’attente Service Bus, consultez [Prise en main des files d’attente Service Bus sur le portail Azure](service-bus-quickstart-portal.md).

### <a name="set-up-a-java-development-environment"></a>Configurer un environnement de développement Java

Pour développer des applications Java, vous devez configurer l’environnement de développement approprié : 
   * Soit le kit JDK (Java Development Kit), soit l’environnent JRE (Java Runtime Environment) est installé.
   * Le kit JDK ou l’environnement JRE est ajouté au chemin du build et aux variables système appropriées.
   * Un environnement de développement intégré Java (Java IDE) est installé pour utiliser le kit JDK ou l’environnement JRE. Par exemple Eclipse ou IntelliJ.

Pour en savoir plus sur la préparation de votre environnement de développement pour Java sur Azure, utilisez [ce guide](/azure/developer/java/fundamentals/).

## <a name="what-jms-features-are-supported"></a>Quelles sont les fonctionnalités JMS prises en charge ?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Téléchargement de la bibliothèque de client Java Message Service (JMS)

Pour utiliser toutes les fonctionnalités disponibles sur Azure Service Bus niveau Premium, la bibliothèque ci-dessous doit être ajoutée au chemin de build du projet.

[Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Pour ajouter [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) au chemin du build, utilisez l’outil de gestion de dépendances préféré pour votre projet, comme [Maven](https://maven.apache.org/) ou [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Codage d’applications Java

Une fois les dépendances importées, les applications Java peuvent être écrites sans dépendre du fournisseur JMS.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Connexion à Azure Service Bus avec JMS

Pour vous connecter à Azure Service Bus à l’aide de clients JMS, il vous faut la **chaine de connexion**, disponible dans les « stratégies d’accès partagé » dans le [Portail Azure](https://portal.azure.com) sous **Chaîne de connexion principale**.

1. Instanciez le `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instanciez le `ServiceBusJmsConnectionFactory` avec le `ServiceBusConnectionString` approprié.

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Utilisez le `ConnectionFactory` pour créer un `Connection` puis un `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    ou un `JMSContext` (pour les clients JMS 2.0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > Bien qu’elles portent le même nom, une « session » JMS et une « session » Service Bus sont complètement indépendantes l’une de l’autre.
    >
    > Dans JMS 1.1, une session est un bloc de construction essentiel de l’API qui permet la création des éléments MessageProducer, MessageConsumer et Message. Pour plus d’informations, consultez le [Modèle de programmation de l’API JMS](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
    >
    > Dans Service Bus, les [sessions](message-sessions.md) sont des constructions côté service et côté client permettant d’activer le traitement FIFO sur les files d’attente et les abonnements.
    >

### <a name="write-the-jms-application"></a>Écrire l’application JMS

Une fois que `Session` ou `JMSContext` a été instancié, votre application peut exploiter les API JMS familières pour effectuer des opérations de gestion et de données.

Reportez-vous à la liste des [fonctionnalités JMS prises en charge](how-to-use-java-message-service-20.md#what-jms-features-are-supported) pour connaître les API prises en charge.

Voici quelques exemples d’extraits de code pour la prise en main de JMS-

#### <a name="sending-messages-to-a-queue-and-topic"></a>Envoi de messages à une rubrique et une file d'attente

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Réception de messages à partir de la file d'attente

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Réception de messages d’un abonnement durable partagé sur une rubrique

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Résumé

Ce guide vous a présenté les différentes interactions possibles entre Azure Service Bus et les applications clientes Java utilisant Java Message Service (JMS) avec AMQP 1.0.

Vous pouvez également utiliser l'AMQP 1.0 de Service Bus depuis d'autres langages, notamment .NET, C, Python et PHP. Les composants intégrés avec ces différents langages peuvent échanger des messages de manière fiable et fidèle en utilisant le support AMQP 1.0 dans Service Bus.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’informations sur Azure Service Bus et de détails sur les entités Java Message Service (JMS), suivez les liens ci-dessous : 
* [Service Bus - Files d’attente, rubriques et abonnements](service-bus-queues-topics-subscriptions.md)
* [Service Bus - entités Java Message Service](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Prise en charge d’AMQP 1.0 dans Service Bus](service-bus-amqp-overview.md)
* [Guide du développeur sur l’utilisation de Service Bus avec AMQP 1.0](service-bus-amqp-dotnet.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [API Java Message Service (doc Oracle externe)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Découvrez comment migrer d’ActiveMQ vers Service Bus](migrate-jms-activemq-to-servicebus.md)