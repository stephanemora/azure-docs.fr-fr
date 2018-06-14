---
title: 'Didacticiel : Apache Spark Structured Streaming avec Kafka - Azure HDInsight | Microsoft Docs'
description: Découvrez comment utiliser la diffusion en continu Apache Spark pour échanger des flux de données avec Apache Kafka. Dans ce didacticiel, vous diffusez des données avec un bloc-notes Jupyter à partir de Spark sur HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: 8c7c1b37102e94f00ac6077958952eb52b342668
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939246"
---
# <a name="tutorial-use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Didacticiel : utiliser Spark Structured Streaming avec Kafka sur HDInsight

Ce didacticiel montre comment utiliser Spark Structured Streaming pour lire et écrire des données avec Apache Kafka sur Azure HDInsight.

Spark Structured Streaming est un moteur de traitement de flux basé sur Spark SQL. Il vous permet d’exprimer des calculs de diffusion en continu de la même façon que pour les calculs de lot sur les données statiques. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Structured streaming avec Kafka
> * Créer des clusters Kafka et Spark
> * Charger le bloc-notes vers Spark
> * Utiliser le bloc-notes
> * Supprimer des ressources

Lorsque vous avez terminé les étapes décrites dans ce document, n’oubliez pas de supprimer les clusters pour éviter les frais supplémentaires.

## <a name="prerequisites"></a>Prérequis


* Connaissances sur l’utilisation des blocs-notes Jupyter Notebook avec Spark sur HDInsight. Pour plus d’informations, consultez le document [Load data and run queries with Spark on HDInsight (Charger des données et exécuter des requêtes interactives avec Spark dans HDInsight)](spark/apache-spark-load-data-run-query.md).

* Connaissances sur le langage de programmation [Scala](https://www.scala-lang.org/). Le code utilisé dans ce didacticiel est écrit dans Scala.

* Connaissances sur la création des rubriques Kafka. Pour plus d’informations, consultez le document [Démarrer avec Apache Kafka sur HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un groupe de ressources Azure contenant à la fois un Spark sur HDInsight et un Kafka sur un cluster HDInsight. Ces clusters sont tous deux situés dans un réseau virtuel Azure, ce qui permet au cluster Spark de communiquer directement avec le cluster Kafka.
> 
> Pour des raisons pratiques, ce document renvoie à un modèle permettant de créer toutes les ressources Azure requises. 
>
> Pour plus d’informations sur l’utilisation de HDInsight dans un réseau virtuel, consultez le document [Étendre HDInsight à l’aide d’un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="structured-streaming-with-kafka"></a>Structured streaming avec Kafka

Spark Structured Streaming est un moteur de traitement de flux basé sur le moteur Spark SQL. Lorsque vous utilisez Structured Streaming, vous pouvez écrire les requêtes de diffusion en continu de la même façon que vous écrivez des requêtes par lot.

Les extraits de code suivants illustrent la lecture à partir de Kafka et le stockage dans le fichier. Le premier est une opération de traitement par lot, tandis que le second est une opération de diffusion en continu :

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()
// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Dans les deux extraits de code, les données sont lues à partir de Kafka et écrites dans le fichier. Voici les différences qui séparent les exemples :

| Batch | Diffusion en continu |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

L’opération de diffusion en continu utilise également `awaitTermination(30000)`, ce qui bloque le flux après 30 000 ms. 

Pour utiliser Structured Streaming avec Kafka, votre projet doit avoir une dépendance sur le package `org.apache.spark : spark-sql-kafka-0-10_2.11`. La version de ce package doit correspondre à la version de Spark sur HDInsight. Pour Spark 2.2.0 (disponible dans HDInsight 3.6), les informations de dépendance relatives à différents types de projet se trouvent sur [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Pour le bloc-notes Jupyter Notebook fourni avec ce didacticiel, la cellule suivante charge cette dépendance de package :

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Création des clusters

Apache Kafka sur HDInsight ne donne pas accès aux répartiteurs Kafka via l’internet public. Tout ce qui utilise Kafka doit se trouver sur le même réseau virtuel Azure. Dans ce didacticiel, les clusters Kafka et Spark se trouvent dans le même réseau virtuel Azure. 

Le diagramme suivant illustre les flux de communication entre Spark et Kafka :

![Diagramme des clusters Spark et Kafka dans un réseau virtuel Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Le service Kafka est limité à la communication au sein du réseau virtuel. L’accès aux autres services sur le cluster, tels que SSH et Ambari, se fait via Internet. Pour plus d’informations sur les ports publics disponibles avec HDInsight, consultez [Ports et URI utilisés par HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pour créer un réseau virtuel Azure puis les clusters Kafka et Spark qu’il contient, procédez comme suit :

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle Azure Resource Manager se trouve dans **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Ce modèle crée les ressources suivantes :

    * Un cluster Kafka sur HDInsight 3.6.
    * Un cluster Spark 2.2.0 sur HDInsight 3.6.
    * Un réseau virtuel Azure, qui contient les clusters HDInsight.

    > [!IMPORTANT]
    > Le bloc-notes de diffusion en continu structurée utilisé dans ce didacticiel nécessite Spark 2.2.0 sur HDInsight 3.6. Si vous utilisez une version antérieure de Spark sur HDInsight, vous recevez des erreurs lors de l’utilisation du bloc-notes.

2. Utilisez les informations suivantes pour renseigner les entrées dans la section **Modèle personnalisé** :

    | Paramètre | Valeur |
    | --- | --- |
    | Abonnement | Votre abonnement Azure |
    | Groupe de ressources | Le groupe de ressources qui contient les ressources. |
    | Lieu | La région Azure dans laquelle sont créées les ressources. |
    | Nom du cluster Spark | Le nom du cluster Spark. Les six premiers caractères doivent être différents du nom de cluster Kafka. |
    | Nom du cluster Kafka | Le nom du cluster Kafka. Les six premiers caractères doivent être différents du nom de cluster Spark. |
    | Nom d’utilisateur de connexion au cluster | Le nom d’utilisateur administrateur pour l’accès aux clusters. |
    | Mot de passe de connexion au cluster | Le mot de passe d’utilisateur administrateur pour l’accès aux clusters. |
    | Nom d’utilisateur SSH | L’utilisateur SSH à créer pour l’accès aux clusters. |
    | Mot de passe SSH | Le mot de passe de l’utilisateur SSH. |
   
    ![Capture d’écran du modèle personnalisé](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Passez en revue les **termes et conditions**, puis cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**

4. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. 

> [!NOTE]
> La création des clusters peut prendre jusqu’à 20 minutes.

## <a name="upload-the-notebook"></a>Charger le bloc-notes

Pour télécharger le bloc-notes à partir du projet pour votre cluster Spark sur HDInsight, procédez comme suit :

1. Téléchargez le projet à partir de [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

1. Dans votre navigateur web, connectez-vous au bloc-notes Jupyter sur votre cluster Spark. Dans l’URL suivante, remplacez `CLUSTERNAME` par le nom de votre cluster __Spark__ :

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Lorsque vous y êtes invité, entrez l’identifiant de connexion (admin) et le mot de passe du cluster utilisés lors de la création du cluster.

2. Dans le coin supérieur droit de la page, utilisez le bouton __Charger__ pour télécharger le fichier __spark-structured-streaming-kafka.ipynb__ sur le cluster. Sélectionnez __Ouvrir__ pour démarrer le chargement.

    ![Utiliser le bouton Charger pour sélectionner et charger un bloc-notes](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Sélectionner le fichier KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Recherchez l’entrée __spark-structured-streaming-kafka.ipynb__ dans la liste des bloc-notes, puis sélectionnez __Charger__ en regard.

    ![Pour charger le bloc-notes, utilisez le bouton Charger pour l’entrée KafkaStreaming.ipynb.](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Utiliser le bloc-notes

Une fois que les fichiers ont été chargés, sélectionnez l’entrée __spark-structured-streaming-kafka.ipynb__ pour ouvrir le bloc-notes. Pour savoir comment utiliser le flux structuré Spark avec Kafka sur HDInsight, suivez les instructions dans le bloc-notes.

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour supprimer les ressources créées par ce didacticiel, vous pouvez supprimer le groupe de ressources. La suppression du groupe de ressources efface également le cluster HDInsight associé et d’autres ressources liées au groupe de ressources.

Pour supprimer le groupe de ressources à l’aide du portail Azure :

1. Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez __Groupes de ressources__ pour afficher la liste de vos groupes de ressources.
2. Recherchez le groupe de ressources à supprimer, puis faites un clic droit sur le bouton __Plus__ (...) se trouvant à droite de la liste.
3. Sélectionnez __Supprimer le groupe de ressources__ et confirmez.

> [!WARNING]
> La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé.
> 
> La suppression d’un cluster Kafka sur HDInsight supprime toutes les données stockées dans Kafka.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à utiliser Spark Structured Streaming pour écrire et lire des données à partir de Kafka sur HDInsight. Utilisez le lien suivant pour découvrir comment utiliser Storm avec Kafka.

> [!div class="nextstepaction"]
> [Utiliser Apache Storm avec Kafka](hdinsight-apache-storm-with-kafka.md)