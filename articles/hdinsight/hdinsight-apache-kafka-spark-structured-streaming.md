---
title: Apache Spark Structured Streaming avec Kafka - Azure HDInsight | Documents Microsoft
description: Découvrez comment utiliser la diffusion en continu Apache Spark (DStream) pour échanger des flux de données avec Apache Kafka. Dans cet exemple, vous diffusez des données à l’aide d’un bloc-notes Jupyter à partir de Spark sur HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Utiliser Spark Structured Streaming avec Kafka sur HDInsight

Découvrez comment utiliser Spark Structured Streaming pour lire des données à partir d’Apache Kafka sur Azure HDInsight.

Spark Structured Streaming est un moteur de traitement de flux basé sur Spark SQL. Il vous permet d’exprimer des calculs de diffusion en continu de la même façon que pour les calculs de lot sur les données statiques. Pour plus d’informations sur Structured Streaming, consultez le [Guide de programmation Structured Streaming [Alpha]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) sur le site Apache.org.

> [!IMPORTANT]
> Cet exemple utilise Spark 2.2 sur HDInsight 3.6.
>
> Les étapes décrites dans ce document créent un groupe de ressources Azure qui contient à la fois un Spark sur HDInsight et un Kafka sur un cluster HDInsight. Ces clusters sont tous deux situés dans un réseau virtuel Azure, ce qui permet au cluster Spark de communiquer directement avec le cluster Kafka.
>
> Lorsque vous avez terminé les étapes décrites dans ce document, n’oubliez pas de supprimer les clusters pour éviter les frais supplémentaires.

## <a name="create-the-clusters"></a>Création des clusters

Apache Kafka sur HDInsight ne donne pas accès aux répartiteurs Kafka via l’internet public. Tout ce qui utilise Kafka doit se trouver sur le même réseau virtuel Azure. Dans ce didacticiel, les clusters Kafka et Spark se trouvent dans le même réseau virtuel Azure. 

Le diagramme suivant illustre les flux de communication entre Spark et Kafka :

![Diagramme des clusters Spark et Kafka dans un réseau virtuel Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Le service Kafka est limité à la communication au sein du réseau virtuel. L’accès aux autres services sur le cluster, tels que SSH et Ambari, se fait via Internet. Pour plus d’informations sur les ports publics disponibles avec HDInsight, consultez [Ports et URI utilisés par HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pour plus de commodité, les étapes suivantes utilisent un modèle Azure Resource Manager pour créer des clusters Kafka et Spark au sein d’un réseau virtuel.

1. Utilisez le bouton suivant pour vous connecter à Azure et ouvrir le modèle dans le portail Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Le modèle Azure Resource Manager se trouve dans **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Ce modèle crée les ressources suivantes :

    * Un cluster Kafka sur HDInsight 3.6.
    * Un cluster Spark 2.2.0 sur HDInsight 3.6.
    * Un réseau virtuel Azure, qui contient les clusters HDInsight.

    > [!IMPORTANT]
    > Le bloc-notes de diffusion en continu structurée utilisé dans cet exemple nécessite Spark sur HDInsight 3.6. Si vous utilisez une version antérieure de Spark sur HDInsight, vous recevez des erreurs lors de l’utilisation du bloc-notes.

2. Utilisez les informations suivantes pour renseigner les entrées dans la section **Modèle personnalisé** :

    | Paramètre | Valeur |
    | --- | --- |
    | Abonnement | Votre abonnement Azure |
    | Groupe de ressources | Le groupe de ressources qui contient les ressources. |
    | Lieu | La région Azure dans laquelle sont créées les ressources. |
    | Nom du cluster Spark | Le nom du cluster Spark. |
    | Nom du cluster Kafka | Le nom du cluster Kafka. |
    | Nom d’utilisateur de connexion au cluster | Le nom d’utilisateur administrateur pour l’accès aux clusters. |
    | Mot de passe de connexion au cluster | Le mot de passe d’utilisateur administrateur pour l’accès aux clusters. |
    | Nom d’utilisateur SSH | L’utilisateur SSH à créer pour l’accès aux clusters. |
    | Mot de passe SSH | Le mot de passe de l’utilisateur SSH. |
   
    ![Capture d’écran du modèle personnalisé](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Pour finir, cochez **Épingler au tableau de bord**, puis sélectionnez **Acheter**. 

> [!NOTE]
> La création des clusters peut prendre jusqu’à 20 minutes.

## <a name="get-the-notebook"></a>Obtenir le bloc-notes

Le code de l’exemple décrit dans ce document est disponible à l’adresse [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Charger les blocs-notes

Pour télécharger le bloc-notes à partir du projet pour votre cluster Spark sur HDInsight, procédez comme suit :

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

Maintenant que vous avez appris à utiliser Spark Structured Streaming, consultez les documents suivants pour en savoir plus sur l’utilisation de Spark et de Kafka :

* [Utilisation de la diffusion en continu Spark (DStream) avec Kafka](hdinsight-apache-spark-with-kafka.md).
* [Prise en main du bloc-notes Jupyter et Spark sur HDInsight](spark/apache-spark-jupyter-spark-sql.md)