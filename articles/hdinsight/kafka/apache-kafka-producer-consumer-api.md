---
title: 'Tutoriel : Utiliser les API de producteur et de consommateur Apache Kafka - Azure HDInsight '
description: Découvrez comment utiliser les API de consommateur et de producteur Apache Kafka avec Kafka sur HDInsight. Dans ce didacticiel, vous allez apprendre à utiliser ces API avec Kafka sur HDInsight à partir d’une application Java.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: 23a676c64ec2788ec4a9b3d61f86529fa437079f
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580380"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutoriel : Utiliser les API de producteur et de consommateur Apache Kafka

Découvrez comment utiliser les API de consommateur et de producteur Apache Kafka avec Kafka sur HDInsight.

L’API de producteur Kafka permet aux applications d’envoyer des flux de données au cluster Kafka. L’API de consommateur Kafka permet aux applications de lire des flux de données à partir du cluster.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configuration de l'environnement de développement
> * Configurer votre environnement de déploiement
> * Comprendre le code
> * Générer et déployer l’application
> * Exécuter l’application sur le cluster

Pour plus d’informations sur les API, consultez la documentation Apache sur l’[API de producteur](https://kafka.apache.org/documentation/#producerapi) et l’[API de consommateur](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Configuration de l'environnement de développement

Les composants suivants doivent être installés dans votre environnement de développement :

* [Java JDK 8](https://aka.ms/azure-jdks), ou un équivalent, par exemple, OpenJDK.

* [Apache Maven](https://maven.apache.org/)

* Un client SSH et la commande `scp`. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Un éditeur de texte ou un IDE Java.

Les variables d’environnement suivantes peuvent être définies lors de l’installation de Java et du Kit de développeur Java (JDK) sur votre station de travail de développement. Toutefois, vous devez vérifier qu’elles existent et qu’elles contiennent les valeurs correctes pour votre système.

* `JAVA_HOME` : doit pointer vers le répertoire d’installation du Kit de développeur Java (JDK).
* `PATH` : doit contenir les chemins d’accès suivants :

    * `JAVA_HOME` (ou le chemin équivalent).
    * `JAVA_HOME\bin` (ou le chemin équivalent).
    * Répertoire d’installation de Maven.

## <a name="set-up-your-deployment-environment"></a>Configurer votre environnement de déploiement

Ce tutoriel nécessite Apache Kafka sur HDInsight 3.6. Pour découvrir comment créer un cluster Kafka sur HDInsight, consultez le document [Démarrer avec Apache Kafka sur HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Comprendre le code

L’exemple d’application se trouve sur [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), dans le sous-répertoire `Producer-Consumer`. L’application se compose principalement de quatre fichiers :

* `pom.xml`: ce fichier définit les dépendances du projet, la version Java et les méthodes d’empaquetage.
* `Producer.java`: ce fichier envoie des phrases aléatoires à Kafka à l’aide de l’API de producteur.
* `Consumer.java`: ce fichier utilise l’API de consommateur pour lire les données de Kafka et les émettre dans STDOUT.
* `Run.java`: interface de ligne de commande utilisée pour exécuter le code producteur et consommateur.

### <a name="pomxml"></a>Pom.xml

Les points importants à comprendre dans le fichier `pom.xml` sont les suivants :

* Dépendances : ce projet repose sur les API de producteur et de consommateur Kafka, fournies par le package `kafka-clients`. Le code XML suivant définit cette dépendance :

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]  
    > L’entrée `${kafka.version}` est déclarée dans la section `<properties>..</properties>` de `pom.xml` et elle est configurée pour la version Kafka du cluster HDInsight.

* Plug-ins : les plug-ins Maven fournissent diverses fonctionnalités. Dans ce projet, les plug-ins suivants sont utilisés :

    * `maven-compiler-plugin`: permet de définir la version Java utilisée par le projet à la version 8. Il s’agit de la version de Java utilisée par HDInsight 3.6.
    * `maven-shade-plugin`: permet de générer un fichier uber jar qui contient cette application, ainsi que toutes les dépendances. Il permet aussi de définir le point d’entrée de l’application, afin que vous puissiez exécuter directement le fichier JAR sans spécifier la classe principale.

### <a name="producerjava"></a>Producer.java

Le producteur communique avec les hôtes du répartiteur Kafka (nœuds de travail) et envoie des données dans une rubrique Kafka. L’extrait de code suivant provient du fichier [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) du [référentiel GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) et montre comment définir les propriétés du producteur :

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Le consommateur communique avec les hôtes de répartition Kafka (nœuds de travail) et lit les enregistrements dans une boucle. L’extrait de code suivant provenant du fichier [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) définit les propriétés du consommateur :

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

Dans ce code, le consommateur est configuré pour procéder à la lecture à partir du début de la rubrique (`auto.offset.reset` a la valeur `earliest`.)

### <a name="runjava"></a>Run.java

Le fichier [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fournit une interface de ligne de commande qui exécute le code producteur ou consommateur. Vous devez fournir les informations relatives à l’hôte de répartition Kafka en tant que paramètre. Vous pouvez également inclure une valeur d’ID de groupe, utilisée par le processus consommateur. Si vous créez plusieurs instances de consommateur à l’aide du même ID de groupe, ces dernières équilibrent la charge lors de la lecture à partir de la rubrique.

## <a name="build-and-deploy-the-example"></a>Générer et déployer l’exemple

1. Téléchargez les exemples à partir de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Accédez à l’emplacement du répertoire `Producer-Consumer` et utilisez la commande suivante :

    ```
    mvn clean package
    ```

    Cette commande crée un répertoire nommé `target`, qui contient un fichier nommé `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Utilisez les commandes suivantes pour copier le fichier `kafka-producer-consumer-1.0-SNAPSHOT.jar` dans votre cluster HDInsight :

    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Lorsque vous y êtes invité, entrez le mot de passe de l’utilisateur SSH.

## <a id="run"></a> Exécuter l’exemple

1. Pour ouvrir une connexion SSH au cluster, utilisez la commande suivante :

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Remplacez **SSHUSER** par l’utilisateur SSH pour votre cluster, et remplacez **CLUSTERNAME** par le nom de votre cluster. Si vous y êtes invité, entrez le mot de passe du compte d’utilisateur SSH. Pour en savoir plus sur l’utilisation de `scp` avec HDInsight, voir [Utiliser SSH avec Hadoop - Azure HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Pour créer les rubriques Kafka utilisées par cet exemple, procédez comme suit :

    1. Pour enregistrer le nom de cluster dans une variable et installer un utilitaire d’analyse JSON (`jq`), utilisez les commandes suivantes. Lorsque vous y êtes invité, entrez le nom du cluster Kafka :
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Pour obtenir les hôtes de répartition Kafka et les hôtes Apache ZooKeeper, utilisez les commandes suivantes. Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion (admin) au cluster.
    
        ```bash
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Pour créer la rubrique `test`, utilisez la commande suivante :

        ```bash
        java -jar kafka-producer-consumer.jar create test $KAFKABROKERS
        ```

3. Pour exécuter le producteur et écrire des données dans la rubrique, utilisez la commande suivante :

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. Une fois que le producteur a terminé, utilisez la commande suivante pour lire à partir de la rubrique :

    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```

    Les enregistrements lus et le nombre d’enregistrements s’affichent.

5. Utilisez __Ctrl + C__ pour quitter le consommateur.

### <a name="multiple-consumers"></a>Consommateurs multiples

Les consommateurs Kafka utilisent un groupe de consommateurs lors de la lecture des enregistrements. L’utilisation du même groupe avec plusieurs consommateurs permet des lectures à charge équilibrée à partir d’une rubrique. Chaque consommateur dans le groupe reçoit une partie des enregistrements.

L’application consommatrice accepte un paramètre utilisé comme ID de groupe. Par exemple, la commande suivante démarre un consommateur à l’aide de l’ID de groupe `mygroup` :

```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Pour voir ce processus en action, utilisez la commande suivante :

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

Cette commande utilise `tmux` pour fractionner le terminal en deux colonnes. Un consommateur est lancé dans chaque colonne, avec la même valeur d’ID de groupe. Une fois que les consommateurs ont terminé la lecture, notez que chacun a lu uniquement une partie des enregistrements. Utilisez __Ctrl + C __ deux fois pour quitter `tmux`.

La consommation par les clients au sein du même groupe est gérée par le biais des partitions de la rubrique. Dans cet exemple de code, la rubrique `test` créée précédemment possède huit partitions. Si vous lancez huit consommateurs, chaque consommateur lit les enregistrements à partir d’une seule partition pour la rubrique.

> [!IMPORTANT]  
> Il ne peut pas y avoir plus d’instances de consommateurs dans un groupe de consommateurs que de partitions. Dans cet exemple, un groupe de consommateurs peut contenir jusqu’à huit consommateurs puisque c’est le nombre de partitions de la rubrique. Vous pouvez également disposer de plusieurs groupes de consommateurs, chacun ne dépassant pas huit consommateurs.

Les enregistrements stockés dans Kafka sont stockés dans l’ordre de réception dans une partition. Pour obtenir la livraison chronologique des enregistrements *dans une partition*, créez un groupe de consommateurs où le nombre d’instances de consommateurs correspond au nombre de partitions. Pour obtenir la livraison chronologique des enregistrements *dans la rubrique*, créez un groupe de consommateurs avec une seule instance de consommateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez découvert comment utiliser les API de consommateur et de producteur Apache Kafka avec Kafka sur HDInsight. Consultez les articles suivants pour en savoir plus sur l’utilisation de Kafka :

* [Analyser les journaux Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Répliquer des données d’un cluster Kafka à un autre](apache-kafka-mirroring.md)
* [API Apache Kafka Streams avec HDInsight](apache-kafka-streams-api.md)
