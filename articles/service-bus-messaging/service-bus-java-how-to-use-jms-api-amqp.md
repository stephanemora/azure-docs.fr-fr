---
title: Utiliser AMQP avec l’API Java Message Service et Azure Service Bus
description: Découvrez comment utiliser Java Message Service (JMS) avec Azure Service Bus et le protocole Advanced Message Queuing Protocol (AMQP) 1.0.
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 95558f973041a086dc66d2bebb1ce54ee58c952a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320780"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>Utiliser Java Message Service (JMS) avec Azure Service Bus et AMQP 1.0
Cet article explique comment utiliser les fonctionnalités de messagerie d’Azure Service Bus (files d’attente et rubriques de publication/d’abonnement) à partir d’applications Java en utilisant la populaire API standard Java Message Service (JMS). Un [article complémentaire](service-bus-amqp-dotnet.md) explique comment réaliser les mêmes opérations à l’aide de l’API .NET Azure Service Bus. Vous pouvez utiliser ces deux guides ensemble pour découvrir la messagerie interplateforme en utilisant AMQP 1.0.

Advanced Message Queuing Protocol (AMQP) 1.0 est un protocole de messagerie « wire-level » efficace et fiable qui peut être utilisé pour créer des applications de messagerie interplateforme robustes.

La prise en charge d'AMQP 1.0 dans Azure Service Bus signifie que vous pouvez utiliser des fonctionnalités de messagerie répartie de mise en file d’attente et de publication/d'abonnement à partir de diverses plateformes, à l'aide d'un protocole binaire efficace. De plus, vous pouvez générer des applications constituées de composants créés à l'aide de divers langages, structures et systèmes d'exploitation.

## <a name="get-started-with-service-bus"></a>Prise en main de Service Bus
Ce guide présuppose que vous disposez déjà d’un espace de noms Service Bus contenant une file d’attente nommée `basicqueue`. Dans le cas contraire, vous pouvez [créer l’espace de noms et la file d’attente](service-bus-create-namespace-portal.md) à l’aide du [Portail Azure](https://portal.azure.com). Pour plus d’informations sur la création d’espaces de noms et de files d’attente Service Bus, consultez [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Les files d’attente et rubriques partitionnées prennent également en charge AMQP. Pour plus d’informations, consultez [Files d’attente et rubriques partitionnées](service-bus-partitioning.md) et [Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente partitionnées Service Bus](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Téléchargement des bibliothèques clientes JMS d’AMQP 1.0
Pour plus d’informations sur l’adresse de téléchargement de la dernière version de la bibliothèque de client Apache Qpid JMS AMQP 1.0, accédez à [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Vous devez ajouter les quatre fichiers JAR suivants de l’archive de distribution Apache Qpid JMS AMQP 1.0 au CLASSPATH Java lors de la création et de l’exécution des applications JMS avec Service Bus :

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[version].jar

> [!NOTE]
> Les versions et les noms JMS JAR peuvent avoir changé. Pour plus d’informations, consultez [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10).

## <a name="coding-java-applications"></a>Codage d’applications Java
### <a name="java-naming-and-directory-interface-jndi"></a>JNDI (Java Naming and Directory Interface)
JMS utilise l’interface JNDI (Java Naming and Directory Interface) pour créer une séparation entre les noms logiques et les noms physiques. Deux types d'objets JMS sont résolus à l'aide de JNDI : ConnectionFactory et Destination. JNDI utilise un modèle de fournisseur auquel vous pouvez connecter différents services d’annuaire afin de gérer les tâches de résolution de noms. La bibliothèque Apache Qpid JMS AMQP 1.0 est dotée d’un simple fournisseur JNDI fondé sur un fichier de propriétés qui est configuré à l’aide d’un fichier de propriétés au format suivant :

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="setup-jndi-context-and-configure-the-connectionfactory"></a>Installer le contexte JNDI et configurer ConnectionFactory

**ConnectionString** référencé dans celui disponible dans les « Stratégies d’accès partagé » dans le [Portail Azure](https://portal.azure.com) sous **Chaîne de connexion principale**
```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>Configurer les files d’attente de destination pour les fournisseurs et les consommateurs
L’entrée qui définit une destination dans le fournisseur JNDI de fichier de propriétés Qpid est au format suivant :

Pour créer la file d’attente de destination pour le fournisseur - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Producer
MessageProducer producer = session.createProducer(queue);
```

Pour créer la file d’attente de destination pour le consommateur - 
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create Consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>Écrire l’application JMS
Il n’existe aucune API ou option particulière nécessaire à l’utilisation de JMS avec Service Bus. Toutefois, il existe quelques restrictions qui seront abordées ultérieurement. Comme dans toute application JMS, il convient de configurer l’environnement JNDI avant de pouvoir résoudre une **ConnectionFactory** et les destinations.

#### <a name="configure-the-jndi-initialcontext"></a>Configurer JNDI InitialContext
L’environnement JNDI est configuré en transmettant une table de hachage d’informations de configuration dans le constructeur de la classe javax.naming.InitialContext. Les deux éléments requis dans la table de hachage sont le nom de classe de la fabrique de contexte initial et l’URL du fournisseur. Le code suivant montre comment configurer l’environnement JNDI afin d’utiliser le fournisseur JNDI fondé sur le fichier de propriétés Qpid avec un fichier de propriétés nommé **servicebus.properties**.

```java
// set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Une simple application JMS utilisant une file d’attente Service Bus
L’exemple de programme suivant envoie JMS TextMessages vers une file d’attente Service Bus avec le nom logique JNDI « QUEUE » et reçoit les messages en retour.

Vous pouvez accéder à toutes les informations relatives au code source et à la configuration dans le guide de démarrage rapide [Azure Service Bus Samples JMS Queue](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart) (Exemples Azure Service Bus de files d’attente JMS).

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS Queue producer into
 * an Azure Service Bus Queue, and receive them with a JMS message consumer.
 * JMS Queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // we create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter
        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create Connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create Session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // receives message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>Exécution de l'application
Passez la **chaîne de connexion** des Stratégies d’accès partagé pour exécuter l’application.
Voici la sortie obtenue avec l’exécution de l’application :

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>Mappage entre une disposition AMQP et une opération Service Bus
Voici comment une disposition AMQP se traduit par une opération Service Bus :

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>Rubriques JMS vs. Rubriques de Service Bus
L'utilisation de rubriques et d'abonnements Azure Service Bus via l'API JMS (Java Message Service) offre des fonctionnalités d'envoi et de réception de base. Il s’agit d’un choix pratique pour déplacer des applications d’autres courtiers de messages avec des API compatibles JMS, même si les rubriques Service Bus sont différentes des rubriques JMS et nécessitent quelques ajustements. 

Les rubriques Azure Service Bus acheminent les messages vers des abonnements nommés, partagés et durables gérés via l’interface de gestion des ressources Azure, les outils de ligne de commande Azure ou le Portail Azure. Chaque abonnement permet jusqu'à 2 000 règles de sélection, chacune pouvant s'accompagner d'une condition de filtre et, pour les filtres SQL, d'une action de transformation des métadonnées. Chaque correspondance de condition de filtre sélectionne le message d’entrée à copier dans l’abonnement.  

La réception de messages provenant d'abonnements est identique à la réception de messages provenant de files d'attente. Chaque abonnement dispose d’une file d’attente de lettres mortes et peut transférer automatiquement les messages vers une autre file d’attente ou d’autres rubriques. 

Avec les rubriques JMS, les clients peuvent dynamiquement créer des abonnés non durables et durables qui permettent éventuellement de filtrer les messages avec des sélecteurs de messages. Ces entités non partagées ne sont pas prises en charge par Service Bus. La syntaxe des règles de filtrage SQL pour Service Bus est toutefois semblable à celle du sélecteur de messages prise en charge par JMS. 

Le côté éditeur de rubrique JMS est compatible avec Service Bus, comme le montre cet exemple, mais les abonnés dynamiques ne le sont pas. Les API JMS liées à la topologie qui suivent ne sont pas prises en charge par Service Bus. 

## <a name="unsupported-features-and-restrictions"></a>Fonctionnalités non prises en charge et restrictions
Les restrictions suivantes existent pour l’utilisation de JMS sur AMQP 1.0 avec Service Bus, à savoir :

* Un seul **MessageProducer** ou **MessageConsumer** est autorisé par **Session**. Si vous devez créer plusieurs **MessageProducers** ou **MessageConsumers** dans une application, créez une **Session** dédiée pour chacun d’eux.
* Les abonnements aux rubriques volatiles ne sont actuellement pas pris en charge.
* Les **MessageSelectors** ne sont actuellement pas pris en charge.
* Les transactions distribuées ne sont pas prises en charge (mais les sessions traitées sont prises en charge).

En outre, Azure Service Bus sépare le plan de contrôle du plan de données et par conséquent ne prend pas en charge plusieurs des fonctions de topologie dynamique de JMS :

| Méthode non prise en charge          | Remplacer par                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | créer un abonnement à une rubrique portage le sélecteur de message                                 |
| createDurableConsumer       | créer un abonnement à une rubrique portage le sélecteur de message                                 |
| createSharedConsumer        | Les rubriques Service Bus peuvent toujours être partagées, voir ci-dessus                                       |
| createSharedDurableConsumer | Les rubriques Service Bus peuvent toujours être partagées, voir ci-dessus                                       |
| createTemporaryTopic        | créer une rubrique via le portail/les outils/l’API de gestion avec *AutoDeleteOnIdle* défini sur une période d’expiration |
| createTopic                 | créer une rubrique via le portail/les outils/l’API de gestion                                           |
| unsubscribe                 | supprimer la rubrique via le portail/les outils/l’API de gestion                                             |
| createBrowser               | non pris en charge. Utiliser la fonctionnalité Peek() de l’API Service Bus                         |
| createQueue                 | créer une file d’attente via le portail/les outils/l’API de gestion                                           | 
| createTemporaryQueue        | créer une file d’attente via le portail/les outils/l’API de gestion avec *AutoDeleteOnIdle* défini sur une période d’expiration |
| receiveNoWait               | utiliser la méthode receive() fournie par le Kit de développement logiciel (SDK) Service Bus et spécifiez un délai d’expiration très court ou égal à zéro |

## <a name="summary"></a>Résumé
Ce manuel d’utilisation vous a montré comment mettre en œuvre les fonctionnalités de messagerie répartie Service Bus (rubriques files d’attente et publication/abonnement) depuis Java en utilisant l’API JMS (Java Message Service) connue et AMQP 1.0.

Vous pouvez également utiliser l'AMQP 1.0 de Service Bus depuis d'autres langages, notamment .NET, C, Python et PHP. Les composants intégrés avec ces différents langages peuvent échanger des messages de manière fiable et fidèle en utilisant le support AMQP 1.0 dans Service Bus.

## <a name="next-steps"></a>Étapes suivantes
* [Prise en charge d’AMQP 1.0 dans Service Bus](service-bus-amqp-overview.md)
* [Utilisation d’AMQP 1.0 avec l’API .NET Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [Guide du développeur sur l’utilisation de Service Bus avec AMQP 1.0](service-bus-amqp-dotnet.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Centre de développement Java](https://azure.microsoft.com/develop/java/)

