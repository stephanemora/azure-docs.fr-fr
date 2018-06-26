---
title: Utilisation d’Akka Streams avec Azure Event Hubs pour l’écosystème Kafka | Microsoft Docs
description: Connexion d’Akka Streams à un Event Hub prenant en charge Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35302794"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>Utilisation d’Akka Streams avec Event Hubs pour l’écosystème Kafka

Un des principaux avantages de l’utilisation d’Apache Kafka est l’écosystème d’infrastructures auquel il peut se connecter. Les Event Hubs prenant en charge Kafka associent la flexibilité de Kafka à l’évolutivité, à la cohérence et à la prise en charge de l’écosystème Azure.

Ce tutoriel vous montre comment connecter Akka Streams à des hubs d’évenements prenant en charge Kafka sans modifier vos protocoles clients ni exécuter vos propres clusters. Azure Event Hubs pour l’écosystème Kafka prend en charge [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vérifiez que les conditions préalables ci-dessous sont bien remplies :

* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargé](http://maven.apache.org/download.cgi) et [installé](http://maven.apache.org/install.html) une archive binaire Maven.
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/downloads)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Un espace de noms Event Hubs est requis pour échanger avec tout service Event Hubs. Pour obtenir des instructions sur l’obtention d’un point de terminaison Kafka pour Event Hubs, voir [Création d’un Event Hub prenant en charge Kafka](event-hubs-create-kafka-enabled.md). Veillez à copier la chaîne de connexion Event Hubs pour une utilisation ultérieure.

## <a name="clone-the-example-project"></a>Cloner l’exemple de projet

À présent que vous avez une chaîne de connexion Event Hubs prenant en charge Kafka, clonez le référentiel Azure Event Hubs et accédez au sous-dossier `akka` :

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Producteur de flux Akka

À l’aide de l’exemple du producteur de flux Akka fourni, envoyez des messages au service Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fournir un point de terminaison Event Hubs Kafka

#### <a name="producer-applicationconf"></a>Producer application.conf

Mettez à jour des valeurs `bootstrap.servers` et `sasl.jaas.config` dans`producer/src/main/resources/application.conf` pour diriger le producteur vers le point de terminaison Event Hubs Kafka avec l’authentification correcte.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Exécuter le producteur depuis la ligne de commande

Pour exécuter le producteur depuis la ligne de commande, générez le fichier JAR, puis exécutez depuis Maven (ou générez le fichier JAR avec Maven, puis exécutez dans Java en ajoutant le ou les fichiers JAR Kafka au paramètre classpath) :

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

le producteur va maintenant commencer à envoyer des événement à l’Event Hub prenant en charge Kafka au niveau de la rubrique `test` et imprimer les événements dans stdout.

## <a name="akka-streams-consumer"></a>Consommateur de flux Akka

À l’aide de l’exemple de contrôle serveur consommateur fourni, recevez des messages à partir des Event Hubs prenant en charge Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fournir un point de terminaison Event Hubs Kafka

#### <a name="consumer-applicationconf"></a>Consumer application.conf

Mettez à jour des valeurs `bootstrap.servers` et `sasl.jaas.config` dans`consumer/src/main/resources/application.conf` pour diriger le contrôle serveur consommateur vers le point de terminaison Event Hubs Kafka avec l’authentification correcte.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Exécuter le contrôle serveur consommateur depuis la ligne de commande

Pour exécuter le contrôle serveur consommateur depuis la ligne de commande, générez le fichier JAR, puis exécutez depuis Maven (ou générez le fichier JAR avec Maven, puis exécutez dans Java en ajoutant le ou les fichiers JAR Kafka au paramètre classpath) :

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Si l’Event Hub prenant en charge Kafka a des événements (par exemple, si votre producteur est également en cours d’exécution), le contrôle serveur consommateur commence maintenant à recevoir les événements provenant de la rubrique `test`. 

Consultez le [Guide Kafka de flux Akka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) pour plus d’informations sur les flux Akka.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)
* [En savoir plus sur Event Hubs pour l’écosystème Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Utilisez [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) pour [diffuser en continu des événements à partir d’un Kafka local vers des Event Hubs prenant en charge Kafka dans le cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* En savoir plus sur les diffusions dans des Event Hubs prenant en charge Kafka à l’aide d’[applications Kafka natives](event-hubs-quickstart-kafka-enabled-event-hubs.md) ou [Apache Flink](event-hubs-kafka-flink-tutorial.md)
