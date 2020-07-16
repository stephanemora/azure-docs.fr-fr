---
title: Migrer des applications JMS (Java Message Service) d’ActiveMQ vers Azure Service Bus | Microsoft Docs
description: Cet article explique comment migrer des applications JMS existantes qui interagissent avec Active MQ pour interagir avec Azure Service Bus.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.openlocfilehash: 3da4f693f4cfec47c5456a0c5998f58f5fe02949
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122185"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-active-mq-to-azure-service-bus"></a>Migrer des applications JMS (Java Message Service) 2.0 existantes d’Active MQ vers Azure Service Bus

Azure Service Bus prend en charge les charges de travail Java/J2EE et Spring qui utilisent l’API Java Message Service (JMS) 2.0 sur le protocole AMQP (Advanced Message Queueing Protocol).

Ce guide décrit ce que vous devez savoir quand vous souhaitez modifier une application JMS 2.0 qui interagit avec un répartiteur JMS (plus spécifiquement Apache ActiveMQ ou Amazon MQ) pour qu’elle interagisse avec Azure Service Bus.

## <a name="before-you-start"></a>Avant de commencer

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Différences entre Azure Service Bus et Apache ActiveMQ

Azure Service Bus et Apache ActiveMQ sont tous deux des répartiteurs de messages qui fonctionnent en tant que fournisseurs JMS avec lesquels les applications clientes échangent des messages. Ils activent tous les deux la sémantique point-à-point avec des **files d’attente** et la sémantique de publication-abonnement avec des **rubriques** et des **abonnements**. 

Il existe toutefois certaines différences.

| Catégorie | Active MQ | Azure Service Bus |
| --- | --- | --- |
| Hiérarchisation des applications | Monolithe en cluster | À deux niveaux <br> (passerelle + back-end) |
| Prise en charge de protocole | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Mode de provisionnement | <ul> <li> IaaS (localement) </li> <li> Amazon MQ (PaaS managé) </li> | PaaS managé |
| Taille des messages | Configurable par le client | 1 Mo (niveau Premium) |
| Haute disponibilité | Managée par le client | Managée par la plateforme |
| Reprise d’activité après sinistre | Managée par le client | Managée par la plateforme | 

### <a name="current-supported-and-unsupported-features"></a>Fonctionnalités prises en charge et non prises en charge actuellement

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="caveats"></a>Mises en garde

La nature à deux niveaux d’Azure Service Bus offre différentes fonctionnalités de continuité d’activité (haute disponibilité et reprise d’activité). Toutefois, certaines considérations sont à prendre en compte lors de l’utilisation des fonctionnalités JMS.

#### <a name="service-upgrades"></a>Mises à niveau de service

En cas de mises à niveau et de redémarrages de Service Bus, les files d’attente ou les rubriques temporaires sont supprimées.

Si l’application est sensible à la perte de données dans les files d’attente ou les rubriques temporaires, nous vous recommandons de ne **pas** utiliser de files d’attente ou de rubriques temporaires, mais plutôt des files d’attente, des rubriques et des abonnements durables.

#### <a name="data-migration"></a>Migration des données

Dans le cadre de la migration/modification de vos applications clientes pour qu’elle interagissent avec Azure Service Bus, les données conservées dans ActiveMQ ne seront pas migrées vers Service Bus.

Une application personnalisée peut être nécessaire pour vider les files d’attente, les rubriques et les abonnements ActiveMQ et relire les messages dans les files d’attente, les rubriques et les abonnements Service Bus.

#### <a name="authentication-and-authorization"></a>Authentification et autorisation

Le contrôle d’accès en fonction du rôle (RBAC) soutenu par Azure Active Directory est le mécanisme d’authentification à privilégier pour Azure Service Bus.

Toutefois, RBAC n’est pas pris en charge actuellement en raison de l’absence de prise en charge de l’authentification basée sur les revendications par Apache QPID JMS.

Pour le moment, l’authentification est prise en charge uniquement avec des clés SAS.

## <a name="pre-migration"></a>Prémigration

### <a name="version-check"></a>Vérification de la version

Voici les composants utilisés lors de l’écriture des applications JMS et les versions spécifiques prises en charge. 

| Composants | Version |
|---|---|
| API JMS (Java Message Service) | 1.1 ou ultérieure |
| Protocole AMQP | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Vérifier que les ports AMQP sont ouverts

Azure Service Bus prend en charge la communication par le biais du protocole AMQP. À cet effet, la communication sur les ports 5671 (AMQP) et 443 (TCP) doit être activée. Selon l’emplacement où les applications clientes sont hébergées, vous pouvez avoir besoin d’un ticket de support pour autoriser la communication sur ces ports.

> [!IMPORTANT]
> Azure Service Bus prend **uniquement** en charge le protocole AMQP 1.0.

### <a name="set-up-enterprise-configurations-vnet-firewall-private-endpoint-etc"></a>Configurations d’entreprise (VNET, pare-feu, point de terminaison privé, etc.)

Azure Service Bus offre différentes fonctionnalités de haute disponibilité et de sécurité de l’entreprise. Pour en savoir plus, suivez les liens de la documentation ci-dessous.

  * [Points de terminaison de service de réseau virtuel](service-bus-service-endpoints.md)
  * [Pare-feu](service-bus-ip-filtering.md)
  * [Chiffrement côté service avec clé gérée par le client (BYOK)](configure-customer-managed-key.md)
  * [Points de terminaison privés](private-link-service.md)
  * [Authentification et autorisation](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Supervision, alertes et suivi

Des métriques sont publiées pour chaque espace de noms Service Bus sur Azure Monitor, et peuvent être exploitées à des fins d’alerte et de mise à l’échelle dynamique des ressources allouées à l’espace de noms.

Pour plus d’informations sur les différentes métriques et sur la configuration des alertes, consultez [Métriques Service Bus dans Azure Monitor](service-bus-metrics-azure-monitor.md).

Consultez également les articles sur le [suivi côté client des opérations de données](service-bus-end-to-end-tracing.md) et la [journalisation opérationnelle/des diagnostics pour les opérations de gestion](service-bus-diagnostic-logs.md).

### <a name="metrics---newrelic"></a>Métriques - NewRelic

Vous trouverez ci-dessous un guide pratique sur le mappage entre les métriques ActiveMQ et les métriques Azure Service Bus. Les informations ci-dessous sont issues de NewRelic.

  * [Métriques NewRelic ActiveMQ/Amazon MQ](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [Métriques NewRelic Azure Service Bus](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Actuellement, NewRelic n’offre pas directement d’intégration transparente avec ActiveMQ, mais des métriques sont disponibles pour Amazon MQ.
> Amazon MQ étant dérivé d’ActiveMQ, le guide ci-dessous mappe les métriques NewRelic d’AmazonMQ à Azure Service Bus.
>

|Regroupement des métriques| Métrique AmazonMQ/Active MQ | Métrique Azure Service Bus |
|------------|---------------------------|--------------------------|
|Service Broker|`CpuUtilization`|`CPUXNS`|
|Service Broker|`MemoryUsage`|`WSXNS`|
|Service Broker|`CurrentConnectionsCount`|`activeConnections`|
|Service Broker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Service Broker|`InactiveDurableTopicSubscribersCount`|Tirer parti des métriques des abonnements|
|Service Broker|`TotalMessageCount`|Tirer parti du niveau File d’attente/Rubrique/Abonnement `activeMessages`|
|File d’attente/Rubrique|`EnqueueCount`|`incomingMessages`|
|File d’attente/Rubrique|`DequeueCount`|`outgoingMessages`|
|File d'attente|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migration

Pour migrer votre application JMS 2.0 existante afin qu’elle interagisse avec Azure Service Bus, vous devez effectuer les étapes ci-dessous.

### <a name="export-topology-from-activemq-and-create-the-entities-in-azure-service-bus-optional"></a>Exporter la topologie à partir d’ActiveMQ et créer les entités dans Azure Service Bus (facultatif)

Pour être sûr que les applications clientes puissent se connecter en toute transparence à Azure Service Bus, vous devez migrer la topologie (qui comprend les files d’attente, les rubriques et les abonnements) d’**Apache ActiveMQ** vers **Azure Service bus**.

> [!NOTE]
> Pour les applications JMS, la création de files d’attente, de rubriques et d’abonnements est une opération de runtime. La plupart des fournisseurs JMS (répartiteurs de messages) offrent des fonctionnalités permettant de créer des *files d’attente*, des *rubriques* et des *abonnements* au moment de l’exécution.
>
> L’étape ci-dessus est par conséquent facultative.
>
> Pour être sûr que votre application dispose des autorisations nécessaires pour créer la topologie au moment de l’exécution, vérifiez que la chaîne de connexion avec les autorisations ***« Gérer » SAS*** est utilisée.

Action à réaliser 
  * Tirer parti des [outils en ligne de commande ActiveMQ](https://activemq.apache.org/activemq-command-line-tools-reference) pour exporter la topologie
  * Recréer la même topologie à l’aide d’un [modèle Azure Resource Manager](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
  * Exécuter le modèle Azure Resource Manager


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importer la dépendance Maven pour l’implémentation JMS Service Bus

Pour garantir une connectivité transparente avec Azure Service Bus, vous devez ajouter le package ***azure-servicebus-jms*** en tant que dépendance au fichier `pom.xml` Maven comme indiqué ci-dessous.

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Modifications de configuration du serveur d’applications

Cette partie est propre au serveur d’applications qui héberge vos applications clientes se connectant à Active MQ.

#### <a name="tomcat"></a>Tomcat

Ici, nous commençons par la configuration propre à Active MQ comme indiqué dans le fichier `/META-INF/context.xml`.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

Ce qui peut être adapté comme indiqué ci-dessous pour pointer vers Azure Service Bus.

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Applications Spring

##### <a name="update-applicationproperties-file"></a>Mettre à jour le fichier `application.properties`

Si vous utilisez une application Spring Boot pour vous connecter à ActiveMQ.

Ici, l’objectif est de ***supprimer*** les propriétés propres à ActiveMQ du fichier `application.properties`.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Ensuite, ***ajoutez*** les propriétés propres à Service Bus au fichier `application.properties`.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-the-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Remplacer ActiveMQConnectionFactory par ServiceBusJmsConnectionFactory

L’étape suivante consiste à remplacer l’instance d’ActiveMQConnectionFactory par ServiceBusJmsConnectionFactory.

> [!NOTE] 
> Les modifications de code réelles sont propres à l’application et à la façon dont les dépendances sont gérées, mais l’exemple ci-dessous fournit des conseils sur ***ce qui doit être modifié***.
>

Auparavant, vous instanciez peut-être un objet d’ActiveMQConnectionFactory comme indiqué ci-dessous.

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Cela serait remplacé par l’instanciation d’un objet ServiceBusJmsConnectionFactory

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Postmigration

Maintenant que vous avez modifié l’application pour qu’elle commence à envoyer et à recevoir des messages d’Azure Service Bus, vous devez vérifier qu’elle fonctionne comme prévu. Une fois cette opération terminée, vous pouvez affiner et moderniser votre pile d’application.

## <a name="next-steps"></a>Étapes suivantes

Tirez parti de [Spring Boot Starter pour Azure Service Bus JMS](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) afin de bénéficier d’une intégration transparente avec Azure Service Bus.

Pour en savoir plus sur la messagerie Service Bus et JMS (Java Message Service), consultez les rubriques suivantes :

* [JMS avec Service Bus](service-bus-java-how-to-use-jms-api-amqp.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
