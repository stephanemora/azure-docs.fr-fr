---
title: Utiliser Apache Flink pour Apache Kafka - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur la connexion d’Apache Flink à un hub d’événements Azure Apache Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: cb964451d25bb381ee9cca84628852a61e4be9b6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090905"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Utilisation d’Apache Flink avec Azure Event Hubs pour Apache Kafka
Ce tutoriel vous montre comment connecter Apache Flink à des Event Hubs prenant en charge Kafka sans modifier vos protocoles clients ni exécuter vos propres clusters. Azure Event Hubs prend en charge [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html).

Un des principaux avantages de l’utilisation d’Apache Kafka est l’écosystème d’infrastructures auquel il peut se connecter. Les Event Hubs prenant en charge Kafka associent la flexibilité de Kafka à l’évolutivité, à la cohérence et à la prise en charge de l’écosystème Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un espace de noms Event Hubs
> * Cloner l’exemple de projet
> * Exécuter le producteur de Flink 
> * Exécuter le consommateur de Flink

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vérifiez que les conditions préalables ci-dessous sont bien remplies :

* Lisez l’article [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Sur Ubuntu, exécutez `apt-get install default-jdk` pour installer le JDK.
    * Veillez à définir la variable d’environnement JAVA_HOME pour qu’elle pointe vers le dossier dans lequel le JDK est installé.
* [Téléchargé](http://maven.apache.org/download.cgi) et [installé](http://maven.apache.org/install.html) une archive binaire Maven.
    * Sur Ubuntu, vous pouvez exécuter `apt-get install maven` pour installer Maven.
* [Git](https://www.git-scm.com/downloads)
    * Sur Ubuntu, vous pouvez exécuter `sudo apt-get install git` pour installer Git.

## <a name="create-an-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Un espace de noms Event Hubs est requis pour échanger avec tout service Event Hubs. Pour obtenir des instructions sur l’obtention d’un point de terminaison Kafka pour Event Hubs, voir [Création d’un Event Hub prenant en charge Kafka](event-hubs-create-kafka-enabled.md). Veillez à copier la chaîne de connexion Event Hubs pour une utilisation ultérieure.

## <a name="clone-the-example-project"></a>Cloner l’exemple de projet

Maintenant que vous disposez d’une chaîne de connexion Event Hubs prenant en charge Kafka, clonez le référentiel Azure Event Hubs pour Kafka, puis accédez au sous-dossier `flink` :

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Exécuter le producteur de Flink

À l’aide de l’exemple du producteur Flink fourni, envoyez des messages au service Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fournir un point de terminaison Event Hubs Kafka

#### <a name="producerconfig"></a>producer.config

Mettez à jour des valeurs `bootstrap.servers` et `sasl.jaas.config` dans`producer/src/main/resources/producer.config` pour diriger le producteur vers le point de terminaison Event Hubs Kafka avec l’authentification correcte.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Exécuter le producteur depuis la ligne de commande

Pour exécuter le producteur depuis la ligne de commande, générez le fichier JAR, puis exécutez depuis Maven (ou générez le fichier JAR avec Maven, puis exécutez dans Java en ajoutant le ou les fichiers JAR Kafka au paramètre classpath) :

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

le producteur va maintenant commencer à envoyer des événement à l’Event Hub prenant en charge Kafka au niveau de la rubrique `test` et imprimer les événements dans stdout.

## <a name="run-flink-consumer"></a>Exécuter le consommateur de Flink

À l’aide de l’exemple de contrôle serveur consommateur fourni, recevez des messages à partir des Event Hubs prenant en charge Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fournir un point de terminaison Event Hubs Kafka

#### <a name="consumerconfig"></a>consumer.config

Mettez à jour des valeurs `bootstrap.servers` et `sasl.jaas.config` dans`consumer/src/main/resources/consumer.config` pour diriger le contrôle serveur consommateur vers le point de terminaison Event Hubs Kafka avec l’authentification correcte.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Exécuter le contrôle serveur consommateur depuis la ligne de commande

Pour exécuter le contrôle serveur consommateur depuis la ligne de commande, générez le fichier JAR, puis exécutez depuis Maven (ou générez le fichier JAR avec Maven, puis exécutez dans Java en ajoutant le ou les fichiers JAR Kafka au paramètre classpath) :

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Si l’Event Hub prenant en charge Kafka a des événements (par exemple, si votre producteur est également en cours d’exécution), le contrôle serveur consommateur commence maintenant à recevoir les événements provenant de la rubrique `test`.

Extraire le [Guide de connecteur Kafka de Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) pour plus d’informations sur la connexion Flink à Kafka.

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à connecter Apache Flink à des Event Hubs prenant en charge Kafka sans modifier vos protocoles clients ni exécuter vos propres clusters. Dans ce didacticiel, vous avez effectué les étapes suivantes : 

> [!div class="checklist"]
> * Créer un espace de noms Event Hubs
> * Cloner l’exemple de projet
> * Exécuter le producteur de Flink 
> * Exécuter le consommateur de Flink

Pour plus d’informations sur Event Hubs et sur Event Hubs pour Kafka, consultez les articles suivants :  

- [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Créer un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create-kafka-enabled.md)
- [Diffuser dans Event Hubs à partir de vos applications Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Mettre en miroir un répartiteur Kafka dans un Event Hub prenant en charge Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connecter Apache Spark à un Event Hub prenant en charge Kafka](event-hubs-kafka-spark-tutorial.md)
- [Intégrer Kafka Connect à un Event Hub prenant en charge Kafka](event-hubs-kafka-connect-tutorial.md)
- [Connecter Akka Streams à un Event Hub prenant en charge Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Explorer des exemples sur GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
