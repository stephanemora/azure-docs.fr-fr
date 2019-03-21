---
title: Utiliser Apache Kafka sur HDInsight avec Azure IoT Hub
description: Apprenez à utiliser Apache Kafka sur HDInsight avec Azure IoT Hub. Le projet Kafka Connect Azure IoT Hub fournit un connecteur source et récepteur pour Kafka. Si le connecteur source est capable de lire des données à partir d’IoT Hub, le connecteur récepteur écrit dans IoT Hub.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: e64490517603687684617ce915e0d3f3e35298e9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093386"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Utiliser Apache Kafka sur HDInsight avec Azure IoT Hub

Découvrez comment utiliser le connecteur [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) pour déplacer des données entre Apache Kafka sur HDInsight et Azure IoT Hub. Dans ce document, vous allez apprendre à exécuter le connecteur IoT Hub à partir d’un nœud de périphérie du cluster.

L’API Kafka Connect vous permet d’implémenter des connecteurs qui extraient des données vers Kafka en continu ou qui transmettent des données de Kafka vers un autre système. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) est un connecteur qui tire (pull) des données à partir d’Azure IoT Hub et les envoie vers Kafka. Il peut aussi transmettre des données de Kafka vers IoT Hub. 

Pour extraire des données à partir d’IoT Hub, vous devez utiliser un connecteur __source__. Pour transmettre des données vers IoT Hub, vous devez utiliser un connecteur __récepteur__. Le connecteur IoT Hub fournit à la fois un connecteur source et un connecteur récepteur.

Le diagramme suivant illustre le flux de données entre Azure IoT Hub et Kafka sur HDInsight avec l’utilisation du connecteur.

![Image montrant les données transitant d’IoT Hub vers Kafka via le connecteur](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Pour plus d’informations sur l’API Connect, consultez [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Un cluster Kafka sur HDInsight. Pour plus d’informations, consultez le document [Démarrer avec Apache Kafka sur HDInsight](apache-kafka-get-started.md).

* Nœud de périphérie dans le cluster Kafka. Pour plus d’informations, consultez le document [Utiliser des nœuds de périphérie avec HDInsight](../hdinsight-apps-use-edge-node.md).

* Azure IoT Hub. Pour ce tutoriel, je vous recommande de consulter le document [Connecter le simulateur en ligne Raspberry Pi à Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* Un client SSH. Les étapes décrites dans ce document utilisent le protocole SSH pour se connecter au cluster. Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Installation du connecteur

1. Téléchargez la dernière version de Kafka Connect pour Azure IoT Hub à partir de [https://github.com/Azure/toketi-kafka-connect-iothub/releases/](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Pour charger le fichier .jar sur le nœud de périphérie de votre cluster Kafka sur HDInsight, utilisez la commande suivante :

    > [!NOTE]  
    > Remplacez `sshuser` par le compte d’utilisateur SSH de votre cluster HDInsight. Remplacez `new-edgenode` par le nom du nœud de périphérie. Remplacez `clustername` par le nom du cluster. Pour plus d’informations sur le point de terminaison SSH pour le nœud de périphérie, consultez le document [Utiliser des nœuds de périphérie avec HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node).

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Une fois la copie du fichier terminée, connectez-vous au nœud de périphérie en utilisant SSH :

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Pour plus d’informations, consultez le document [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Pour installer le connecteur dans le répertoire `libs` de Kafka, utilisez la commande suivante :

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]  
> Si vous rencontrez des problèmes en utilisant un fichier .jar prédéfini dans les étapes restantes de ce document, essayez de créer le package à partir de la source.
>
> Pour créer le connecteur, vous devez disposer d’un environnement de développement Scala avec l’[outil de génération Scala](https://www.scala-sbt.org/).
>
> 1. Téléchargez la source du connecteur dans votre environnement de développement à partir de [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/).
>
> 2. À partir d’une invite de commandes dans le répertoire du projet, utilisez la commande suivante pour générer et empaqueter le projet :
>
>    ```bash
>    sbt assembly
>    ```
>
>    Cette commande crée un fichier nommé `kafka-connect-iothub-assembly_2.11-0.6.jar` dans le répertoire `target/scala-2.11` du projet.

## <a name="configure-apache-kafka"></a>Configurer Apache Kafka

À partir d’une connexion SSH au nœud de périphérie, effectuez les étapes suivantes pour configurer Kafka de sorte que le connecteur s’exécute en mode autonome :

1. Enregistrez le nom du cluster dans une variable. L’utilisation d’une variable permet de copier/coller plus facilement les autres commandes de cette section :

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Installez l’utilitaire `jq`. Cet utilitaire simplifie le traitement des documents JSON retournés par les requêtes Ambari :

    ```bash
    sudo apt -y install jq
    ```

3. Procurez-vous l’adresse des répartiteurs Kafka. Il se peut que votre cluster en contienne un grand nombre, mais vous n’avez besoin d’en référencer qu’un ou deux. Pour obtenir l’adresse de deux hôtes répartiteurs, utilisez la commande suivante :

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Lorsque vous y êtes invité, entrez le mot de passe du compte de connexion (admin) au cluster. Les informations renvoyées sont similaire au texte suivant :

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Procurez-vous l’adresse des nœuds Apache Zookeeper. Le cluster en contient plusieurs, mais vous n’avez besoin d’en référencer qu’un ou deux. Pour obtenir l’adresse de deux nœuds Zookeeper, utilisez la commande suivante :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Pendant l’exécution du connecteur en mode autonome, le fichier `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` sert à communiquer avec les répartiteurs Kafka. Pour modifier le fichier `connect-standalone.properties`, utilisez la commande suivante :

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Pour définir la configuration autonome de telle sorte que le nœud de périphérie recherche les répartiteurs Kafka, repérez la ligne qui commence par `bootstrap.servers=`. Remplacez la valeur `localhost:9092` par les hôtes répartiteurs de l’étape précédente.

    * Modifiez les lignes `key.converter=` et `value.converter=` en utilisant les valeurs suivantes :

        ```ini
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]  
        > Cette modification vous permet de tester l’utilisation du producteur de console fourni avec Kafka. Vous aurez peut-être besoin de convertisseurs différents pour les autres producteurs et consommateurs. Pour obtenir des informations sur l’utilisation d’autres valeurs de convertisseur, consultez [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Ajoutez une ligne à la fin du fichier qui contient le texte suivant :

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]  
        > Cette modification vise à empêcher les dépassements de délai dans le connecteur récepteur en le limitant à 10 enregistrements à la fois. Pour plus d’informations, consultez [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Pour enregistrer le fichier, appuyez sur __Ctrl+X__, sur __O__, puis sur __Entrée__.

7. Pour créer les rubriques utilisées par le connecteur, utilisez les commandes suivantes :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Pour vérifier que les rubriques `iotin` et `iotout` existent, utilisez la commande suivante :

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    La rubrique `iotin` sert à recevoir les messages en provenance d’IoT Hub. La rubrique `iotout` sert à l’envoi de messages à destination d’IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Obtenir les informations de connexion d’IoT Hub

Pour récupérer les informations d’IoT Hub utilisées par le connecteur, procédez comme suit :

1. Obtenez le point de terminaison compatible Event Hub et le nom du point de terminaison compatible Event Hub pour votre IoT Hub. Pour obtenir ces informations, utilisez l’une des méthodes suivantes :

   * __À partir du [portail Microsoft Azure](https://portal.azure.com/)__, procédez comme suit :

     1. Accédez à votre IoT Hub, puis sélectionnez __Points de terminaison__.
     2. À partir de __Points de terminaison intégrés__, sélectionnez __Événements__.
     3. À partir de __Propriétés__, copiez la valeur des champs suivants :

         * __Nom compatible avec les hubs d’événements__
         * __Point de terminaison compatible avec les hubs d’événements__
         * __Partitions__

        > [!IMPORTANT]  
        > La valeur de point de terminaison en provenance du portail peut contenir du texte superflu qui n’est pas utile dans cet exemple. Extrayez le texte qui correspond à ce modèle `sb://<randomnamespace>.servicebus.windows.net/`.

   * __À partir d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, utilisez la commande suivante :

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Remplacez `myhubname` par le nom de votre IoT Hub. Vous obtenez une réponse semblable au texte suivant :

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Procurez-vous la __stratégie d’accès partagé__ et la __clé__. Pour cet exemple, utilisez la clé de __service__. Pour obtenir ces informations, utilisez l’une des méthodes suivantes :

    * __À partir du [portail Microsoft Azure](https://portal.azure.com/)__, procédez comme suit :

        1. Sélectionnez __Stratégies d’accès partagé__, puis __service__.
        2. Copiez la valeur __Clé primaire__.
        3. Copiez la valeur __Clé primaire de la chaîne de connexion__.

    * __À partir d’[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, utilisez la commande suivante :

        1. Pour obtenir la valeur de la clé primaire, utilisez la commande suivante :

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Remplacez `myhubname` par le nom de votre IoT Hub. La réponse est la clé primaire de la stratégie `service` de ce hub.

        2. Pour obtenir la chaîne de connexion de la stratégie `service`, utilisez la commande suivante :

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Remplacez `myhubname` par le nom de votre IoT Hub. Vous obtenez en réponse la chaîne de connexion de la stratégie `service`.

## <a name="configure-the-source-connection"></a>Configurer la connexion source

Pour configurer la source de telle sorte qu’elle fonctionne avec votre IoT Hub, effectuez les actions suivantes à partir d’une connexion SSH au nœud de périphérie :

1. Créez une copie du fichier `connect-iot-source.properties` dans le répertoire `/usr/hdp/current/kafka-broker/config/`. Pour télécharger le fichier à partir du projet toketi-kafka-connect-iothub, utilisez la commande suivante :

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Pour modifier le fichier `connect-iot-source.properties` et ajouter les informations d’IoT Hub, utilisez la commande suivante :

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Dans l’éditeur, recherchez et modifiez les entrées suivantes :

   * `Kafka.Topic=PLACEHOLDER`: Remplacez  par `iotin`. Les messages reçus d’IoT Hub sont placés dans la rubrique `iotin`.
   * `IotHub.EventHubCompatibleName=PLACEHOLDER`: remplacez `PLACEHOLDER` par le nom compatible Event Hub.
   * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: remplacez `PLACEHOLDER` par le point de terminaison compatible Event Hub.
   * `IotHub.Partitions=PLACEHOLDER`: remplacez `PLACEHOLDER` par le nombre de partitions des étapes précédentes.
   * `IotHub.AccessKeyName=PLACEHOLDER`: Remplacez  par `service`.
   * `IotHub.AccessKeyValue=PLACEHOLDER`: remplacez `PLACEHOLDER` par la clé primaire de la stratégie `service`.
   * `IotHub.StartType=PLACEHOLDER`: remplacez `PLACEHOLDER` par une date UTC. Cette date correspond au moment où le connecteur commence à vérifier la présence de messages. Le format de date est `yyyy-mm-ddThh:mm:ssZ`.
   * `BatchSize=100`: Remplacez  par `5`. Cette modification amène le connecteur à lire les messages dans Kafka dès qu’il y a cinq nouveaux messages dans IoT Hub.

     Pour voir un exemple de configuration, consultez [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Pour enregistrer les modifications, appuyez sur __Ctrl + X__, sur __O__, puis sur __Entrée__.

Pour plus d’informations sur la configuration de la source du connecteur, consultez [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Configurer la connexion du récepteur

Pour configurer la connexion du récepteur de telle sorte qu’elle fonctionne avec votre IoT Hub, effectuez les actions suivantes à partir d’une connexion SSH au nœud de périphérie :

1. Créez une copie du fichier `connect-iothub-sink.properties` dans le répertoire `/usr/hdp/current/kafka-broker/config/`. Pour télécharger le fichier à partir du projet toketi-kafka-connect-iothub, utilisez la commande suivante :

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Pour modifier le fichier `connect-iothub-sink.properties` et ajouter les informations d’IoT Hub, utilisez la commande suivante :

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    Dans l’éditeur, recherchez et modifiez les entrées suivantes :

   * `topics=PLACEHOLDER`: Remplacez  par `iotout`. Les messages écrits dans la rubrique `iotout` sont transférés à l’IoT Hub.
   * `IotHub.ConnectionString=PLACEHOLDER`: remplacez `PLACEHOLDER` par la chaîne de connexion de la stratégie `service`.

     Pour voir un exemple de configuration, consultez [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Pour enregistrer les modifications, appuyez sur __Ctrl + X__, sur __O__, puis sur __Entrée__.

Pour plus d’informations sur la configuration du récepteur du conteneur, consultez [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Démarrer le connecteur source

Pour démarrer le connecteur source, utilisez la commande suivante à partir d’une connexion SSH au nœud de périphérie :

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Une fois le connecteur démarré, envoyez des messages à l’IoT Hub à partir de votre/vos appareil(s). À mesure que le connecteur lit les messages de l’IoT Hub et les stocke dans la rubrique Kafka, il consigne des informations dans la console :

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]  
> Plusieurs avertissements peuvent s’afficher au démarrage du connecteur. Ces avertissements ne posent pas de problèmes pour la réception de messages en provenance de l’IoT Hub.

Pour arrêter le connecteur, appuyez sur __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Démarrer le connecteur récepteur

À partir d’une connexion SSH au nœud de périphérie, utilisez la commande suivante pour démarrer le connecteur récepteur en mode autonome :

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Pendant l’exécution du connecteur, des informations semblables au texte suivant s’affichent à l’écran :

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Vous pouvez constater la présence de plusieurs avertissements pendant le démarrage du connecteur. Vous pouvez ignorer ces erreurs.

Pour envoyer des messages via le connecteur, procédez comme suit :

1. Ouvrez une autre session SSH pour le cluster Kafka :

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. Pour envoyer des messages à la rubrique `iotout`, utilisez la commande suivante :

    > [!WARNING]  
    > Comme il s’agit d’une nouvelle connexion SSH, la variable `$KAFKABROKERS` ne contient pas d’informations. Pour la définir, utilisez l’une des méthodes suivantes :
    >
    > * Utilisez les trois premières étapes de la section [Configurer Apache Kafka](#configure-apache-kafka).
    > * Utilisez la commande `echo $KAFKABROKERS` de la connexion SSH précédente pour obtenir les valeurs, puis remplacez `$KAFKABROKERS` dans la commande suivante par les valeurs réelles.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Cette commande ne vous renvoie pas à l’invite Bash normale. Au lieu de cela, elle envoie l’entrée clavier à la rubrique `iotout`.

3. Pour envoyer un message à votre appareil, collez un document JSON dans la session SSH pour `kafka-console-producer`.

    > [!IMPORTANT]  
    > Vous devez définir la valeur de l’entrée `"deviceId"` en lui attribuant l’ID de votre appareil. Dans l’exemple suivant, l’appareil se nomme `fakepi` :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Le schéma de ce document JSON est décrit de façon plus détaillée à l’adresse [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Si vous utilisez l’appareil Raspberry Pi simulé et qu’il est en cours d’exécution, le message suivant est consigné par l’appareil :

    ```text
    Receive message: Turn On
    ```

    Renvoyez le document JSON, mais modifiez la valeur de l’entrée `"message"`. La nouvelle valeur est consignée par l’appareil.

Pour plus d’informations sur l’utilisation du connecteur récepteur, consultez [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à utiliser l’API Apache Kafka Connect pour démarrer le connecteur IoT Kafka sur HDInsight. Utilisez les liens suivants pour découvrir d’autres façons de travailler avec Kafka :

* [Utiliser Apache Spark avec Apache Kafka sur HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Utiliser Apache Storm avec Apache Kafka sur HDInsight](../hdinsight-apache-storm-with-kafka.md)
