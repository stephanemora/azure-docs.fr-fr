---
title: Utiliser AMQP avec l’API Java Message Service et Azure Service Bus
description: Découvrez comment utiliser Java Message Service (JMS) avec Azure Service Bus et le protocole Advanced Message Queuing Protocol (AMQP) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119155"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Utiliser Java Message Service (JMS) avec Azure Service Bus et AMQP 1.0

La prise en charge d’**Advanced Message Queuing Protocol (AMQP) 1.0**  dans Azure Service Bus signifie que vous pouvez utiliser des fonctionnalités de messagerie répartie de mise en file d’attente et de publication/d’abonnement à partir de diverses plateformes, à l’aide d’un protocole binaire efficace. De plus, vous pouvez générer des applications constituées de composants créés à l'aide de divers langages, structures et systèmes d'exploitation.

Cet article explique comment utiliser les fonctionnalités de messagerie d’Azure Service Bus (files d’attente et rubriques de publication/d’abonnement) à partir d’applications Java en utilisant l’API populaire **Java Message Service (JMS)** par le biais du protocole AMQP.

## <a name="get-started-with-service-bus"></a>Prise en main de Service Bus
Ce guide présuppose que vous disposez déjà d’un espace de noms Service Bus contenant une file d’attente nommée `basicqueue`. Dans le cas contraire, vous pouvez [créer l’espace de noms et la file d’attente](service-bus-create-namespace-portal.md) à l’aide du [Portail Azure](https://portal.azure.com). Pour plus d’informations sur la création d’espaces de noms et de files d’attente Service Bus, consultez [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Les files d’attente et rubriques partitionnées prennent également en charge AMQP. Pour plus d’informations, consultez [Files d’attente et rubriques partitionnées](service-bus-partitioning.md) et [Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente partitionnées Service Bus](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
>

## <a name="what-jms-features-are-supported"></a>Quelles sont les fonctionnalités JMS prises en charge ?

Voici les fonctionnalités JMS prises en charge dans Azure Service Bus.

| Fonctionnalités | Niveau Standard d’Azure Service Bus - JMS 1.1 | Niveau Premium d’Azure Service Bus - JMS 2.0 (version préliminaire) |
|---|---|---|
| Création automatique d’entités avec AMQP | Non pris en charge | **Pris en charge** |
| Files d’attente | **Pris en charge** | **Pris en charge** |
| Rubriques | **Pris en charge** | **Pris en charge** |
| Files d’attente temporaires | Non pris en charge <br/> (Créez une file d’attente normale avec la propriété *AutoDeleteOnIdle* définie à la place) | **Pris en charge** |
| Rubriques temporaires | Non pris en charge | **Pris en charge** |
| Sélecteurs de messages | Non pris en charge | **Pris en charge** |
| Explorateurs de files d’attente | Non pris en charge <br/> (Utilisez la fonctionnalité *Peek* de l’API Service Bus) | **Pris en charge** |
| Abonnements durables partagés | **Pris en charge** | **Pris en charge**|
| Abonnements durables non partagés | Non pris en charge | **Pris en charge** |
| Abonnements non durables partagés | Non pris en charge | **Pris en charge** |
| Abonnements non durables non partagés | Non pris en charge | **Pris en charge** |
| Se désabonner des abonnements durables | Non pris en charge | **Pris en charge** |
| ReceiveNoWait | Non pris en charge | **Pris en charge** |
| Transactions distribuées | Non pris en charge | Non pris en charge |
| Terminus durable | Non pris en charge | Non pris en charge |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>Avertissements supplémentaires pour le niveau Standard de Service Bus
Un seul **MessageProducer** ou **MessageConsumer** est autorisé par **Session**. Si vous devez créer plusieurs **MessageProducers** ou **MessageConsumers** dans une application, créez une **Session** dédiée pour chacun d’eux.

## <a name="downloading-the-java-message-service-jms-client-library"></a>Téléchargement de la bibliothèque de client Java Message Service (JMS)

Pour vous connecter à Azure Service Bus et tirer parti de l’API Java Message Service (JMS) avec AMQP, vous devez exploiter les bibliothèques ci-dessous. Elles doivent être ajoutées au chemin de build à l’aide de l’outil de gestion des dépendances privilégié pour votre projet.

La bibliothèque de client requise dépend du niveau tarifaire utilisé.

### <a name="premium-tier---jms-20-over-amqp-preview"></a>Niveau Premium - JMS 2.0 avec AMQP (version préliminaire)

Pour exploiter toutes les fonctionnalités d’évaluation disponibles avec le niveau Premium d’Azure Service Bus, utilisez la bibliothèque [Azure-servicebus-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).

### <a name="standard-tier---jms-11-over-amqp"></a>Niveau Standard - JMS 1.1 avec AMQP

Pour exploiter les fonctionnalités JMS prises en charge par le niveau Standard de Service Bus (consultez [Quelles sont les fonctionnalités JMS prises en charge ?](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)), utilisez les bibliothèques suivantes :

* [Geronimo JMS 1.1 spec](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Client Qpid JMS](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> Les versions et les noms JMS JAR peuvent avoir changé. Pour plus d’informations, consultez [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).
>

## <a name="coding-java-applications"></a>Codage d’applications Java

Une fois les dépendances importées, les applications Java peuvent être écrites sans dépendre du fournisseur JMS.

Étant donné qu’Azure Service Bus Standard et Premium diffèrent pour ce qui est des dépendances et du nombre de fonctionnalités JMS prises en charge, le modèle de programmation est légèrement différent pour les deux.

> [!IMPORTANT]
> Le guide ci-dessous indique comment se connecter à Azure Service Bus à partir d’une application simple.
>
> Étant donné que la plupart des architectures d’applications d’entreprise peuvent avoir une méthode personnalisée pour gérer les dépendances et les configurations, utilisez la procédure ci-dessous pour comprendre ce qui est nécessaire et vous adapter à votre application de manière appropriée.
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>Connexion à Azure Service Bus avec JMS

Pour vous connecter à Azure Service Bus à l’aide de clients JMS, il vous faut le **ConnectionString** qui est disponible dans les « stratégies d’accès partagé » dans le [Portail Azure](https://portal.azure.com) sous **Chaîne de connexion principale**.


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>Connexion à Azure Service Bus Premium avec JMS 2.0 (version préliminaire)

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

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>Connexion à Azure Service Bus Standard avec JMS 1.1

1. Insérez la configuration Azure Service Bus dans le fichier de propriétés JNDI nommé **servicebus.properties**.
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. Installez le contexte JNDI et configurez ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. Utilisez le `ConnectionFactory` pour créer un `Connection` puis un `Session`.
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>Écrire l’application JMS

Une fois que le `Session` ou le `JMSContext` a été instancié, votre application peut exploiter les API JMS familières pour effectuer des opérations de gestion et de données.

Veuillez vous reporter à la liste des [fonctionnalités JMS prises en charge](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported) pour les niveaux Standard et Premium afin de déterminer les API qui seront prises en charge dans chaque niveau.

## <a name="summary"></a>Résumé
Ce guide vous a présenté les différentes interactions possibles entre Azure Service Bus et les applications clientes Java utilisant Java Message Service (JMS) avec AMQP 1.0.

Vous pouvez également utiliser l'AMQP 1.0 de Service Bus depuis d'autres langages, notamment .NET, C, Python et PHP. Les composants intégrés avec ces différents langages peuvent échanger des messages de manière fiable et fidèle en utilisant le support AMQP 1.0 dans Service Bus.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir plus d’informations sur Azure Service Bus et de détails sur les entités Java Message Service (JMS), suivez les liens ci-dessous : 
* [Service Bus - Files d’attente, rubriques et abonnements](service-bus-queues-topics-subscriptions.md)
* [Service Bus - entités Java Message Service](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Prise en charge d’AMQP 1.0 dans Service Bus](service-bus-amqp-overview.md)
* [Guide du développeur sur l’utilisation de Service Bus avec AMQP 1.0](service-bus-amqp-dotnet.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)

