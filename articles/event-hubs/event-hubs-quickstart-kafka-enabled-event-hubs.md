---
title: Échanger des données avec Azure Event Hubs pour l’écosystème Kafka | Microsoft Docs
description: Échangez des données avec Event Hubs en utilisant le protocole et les API Kafka.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: bahariri
ms.openlocfilehash: 8ef6240d19ce1ac1b891c95ce525a8bd211a2900
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297221"
---
# <a name="stream-into-event-hubs-for-the-kafka-ecosystem"></a>Échanger des données avec Event Hubs pour l’écosystème Kafka

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs).

Ce guide de démarrage rapide montre comment échanger des données avec Event Hubs prenant en charge Kafka, sans changer vos protocoles clients ni exécuter vos propres clusters. Vous découvrez comment utiliser vos producteurs et vos consommateurs pour communiquer avec Event Hubs prenant en charge Kafka, avec seulement une modification de configuration dans vos applications. Azure Event Hubs pour l’écosystème Kafka prend en charge [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous avez :

* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Téléchargez](http://maven.apache.org/download.cgi) et [installez](http://maven.apache.org/install.html) une archive binaire Maven.
* [Git](https://www.git-scm.com/)
* [Un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Envoyer et recevoir des messages avec Kafka dans Event Hubs

1. Clonez le [dépôt Azure Event Hubs](https://github.com/Azure/azure-event-hubs).

2. Accédez à `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Mettez à jour les détails de configuration pour le producteur dans `src/main/resources/producer.config` comme suit :

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Exécutez le code du producteur et échangez les données avec Event Hubs prenant en charge Kafka :
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Accédez à `azure-event-hubs/samples/kafka/quickstart/consumer`.

6. Mettez à jour les détails de configuration pour le consommateur dans `src/main/resources/consumer.config` comme suit :
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Exécutez le code du consommateur et effectuez le traitement depuis Event Hubs prenant en charge Kafka en utilisant vos clients Kafka :

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Si votre cluster Kafka Event Hubs a des événements, vous devez maintenant commencer à les recevoir du consommateur.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur Event Hubs](event-hubs-what-is-event-hubs.md)
* [En savoir plus sur Event Hubs pour l’écosystème Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Utilisez [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) pour [diffuser en continu des événements à partir d’un Kafka local vers des hubs d’événements compatibles avec Kafka dans le cloud.](event-hubs-kafka-mirror-maker-tutorial.md)
* En savoir plus sur les diffusions dans des hubs d’événements compatibles avec Kafka à l’aide des flux [Apache Flink](event-hubs-kafka-flink-tutorial.md) ou [Akka](event-hubs-kafka-akka-streams-tutorial.md).
