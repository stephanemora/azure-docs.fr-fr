---
title: Échanger des données avec Azure Event Hubs pour Apache Kafka | Microsoft Docs
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
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: 2fbca7179d8bc64a92f79f6eaced8d4f1666c530
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069214"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Diffuser en continu dans Event Hubs pour Apache Kafka
Ce guide de démarrage rapide montre comment échanger des données avec Event Hubs prenant en charge Kafka, sans changer vos protocoles clients ni exécuter vos propres clusters. Vous découvrez comment utiliser vos producteurs et vos consommateurs pour communiquer avec Event Hubs prenant en charge Kafka, avec seulement une modification de configuration dans vos applications. Azure Event Hubs prend en charge [Apache Kafka version 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Cet exemple est disponible sur [GitHub](https://github.com/Azure/azure-event-hubs).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vérifiez que vous avez :

* Lisez l’article [Event Hubs pour Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Téléchargez](http://maven.apache.org/download.cgi) et [installez](http://maven.apache.org/install.html) une archive binaire Maven.
* [Git](https://www.git-scm.com/)
* [Un espace de noms Event Hubs prenant en charge Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Créer un espace de noms Event Hubs prenant en charge Kafka

1. Connectez-vous au [portail Azure][portail Azure], puis cliquez sur **Créer une ressource** en haut à gauche de l’écran.

2. Recherchez Event Hubs et sélectionnez les options indiquées ici :
    
    ![Rechercher Event Hubs dans le portail](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Spécifiez un nom unique et activez Kafka sur l’espace de noms. Cliquez sur **Créer**.
    
    ![Créer un espace de noms](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Une fois l’espace de noms créé, sous l’onglet **Paramètres**, cliquez sur **Stratégies d’accès partagé** pour obtenir la chaîne de connexion.

    ![Cliquer sur Stratégies d’accès partagé](./media/event-hubs-create/create-event-hub7.png)

5. Vous pouvez choisir **RootManageSharedAccessKey** par défaut ou ajouter une nouvelle stratégie. Cliquez sur le nom de la stratégie et copiez la chaîne de connexion. 
    
    ![Sélectionner une stratégie](./media/event-hubs-create/create-event-hub8.png)
 
6. Ajoutez cette chaîne de connexion à la configuration de votre application Kafka.

Vous pouvez désormais diffuser des événements à partir de vos applications qui utilisent le protocole Kafka dans Event Hubs.

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
Dans cet article, vous avez appris à échanger des données avec Event Hubs prenant en charge Kafka, sans changer vos protocoles clients ni exécuter vos propres clusters. Pour en savoir plus, passez au tutoriel suivant :

> [!div class="nextstepaction"]
> [Utiliser Kafka MirrorMaker avec Event Hubs](event-hubs-kafka-mirror-maker-tutorial.md)
