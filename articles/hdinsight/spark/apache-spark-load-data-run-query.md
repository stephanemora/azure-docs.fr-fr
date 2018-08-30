---
title: 'Didacticiel : charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight '
description: Découvrez comment charger des données et exécuter des requêtes interactives sur des clusters Spark dans Azure HDInsight.
services: azure-hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: jasonh
ms.date: 05/17/2018
ms.openlocfilehash: d59f04c5dde522f3d193f345ac59147ece9d86f0
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047555"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Didacticiel : charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight

Dans ce didacticiel, vous apprenez à créer une trame de données à partir d’un fichier CSV et comment exécuter des requêtes interactives SQL Spark sur un cluster Apache Spark dans Azure HDInsight. Dans Spark, une trame de données est une collection distribuée de données organisées en colonnes nommées. D’un point de vue conceptuel, une trame de données équivaut à une table d’une base de données relationnelle ou à une trame de données dans R/Python.
 
Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une trame de données à partir d’un fichier CSV
> * Exécuter des requêtes sur la trame de données

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* Effectuez la procédure décrite dans [Créer un cluster Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Créer une trame de données à partir d’un fichier CSV

Les applications peuvent créer des trames de données à partir d’un RDD (Resilient Distributed Dataset) existant, d’une table Hive ou de sources de données à l’aide de l’objet SQLContext. La capture d’écran suivante montre un instantané du fichier HVAC.csv utilisé dans ce didacticiel. Le fichier CSV est fourni avec tous les clusters HDInsight Spark. Les données capturent les variations de température de certains bâtiments.
    
![Instantané des données pour les requêtes Spark SQL interactives](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "instantané des données pour les requêtes Spark SQL interactives")


1. Ouvrez le bloc-notes Jupyter que vous avez créé dans la section des prérequis.
2. Collez l’exemple de code suivant dans une cellule vide du bloc-notes, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. Le code importe les types requis pour ce scénario :

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Lorsque vous exécutez une requête interactive dans Jupyter, la fenêtre du navigateur web ou la légende d’onglet affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

    ![État de la requête interactive Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "État de la requête interactive Spark SQL")

3. Exécutez le code suivant pour créer une trame de données et une table temporaire (**hvac**). 

    ```PySpark
    # Create an RDD from sample data
    csvFile = spark.read.csv('wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

    > [!NOTE]
    > Lorsque vous utilisez le noyau PySpark pour créer un bloc-notes, les contextes SQL sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous n’avez pas besoin de créer explicitement de contextes.


## <a name="run-queries-on-the-dataframe"></a>Exécuter des requêtes sur la trame de données

Une fois la table créée, vous pouvez exécuter une requête interactive sur les données.

1. Exécutez le code suivant dans une cellule vide du bloc-notes :

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Étant donné que le noyau PySpark est utilisé dans le bloc-notes, vous pouvez maintenant exécuter directement une requête SQL interactive sur la table temporaire **hvac**.

   La sortie sous forme de tableau suivante s’affiche.

     ![Sortie sous forme de tableau d’un résultat de requête interactive Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Sortie sous forme de tableau d’un résultat de requête interactive Spark")

3. Vous pouvez également voir les résultats dans d’autres visualisations. Pour afficher un graphique en aires pour le même résultat, sélectionnez **Area** (Aires), puis définissez d’autres valeurs comme indiqué.

    ![Résultat de requête interactive Spark sous forme graphique](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Résultat de requête interactive Spark sous forme graphique")

10. Dans le menu **File** (Fichier) du bloc-notes, sélectionnez **Save and Checkpoint** (Enregistrer et point de contrôle). 

11. Si vous démarrez le [didacticiel suivant](apache-spark-use-bi-tools.md) maintenant, laissez le bloc-notes ouvert. Dans le cas contraire, arrêtez le bloc-notes pour libérer les ressources de cluster : dans le menu **File** (Fichier) du bloc-notes, sélectionnez **Close and Halt** (Fermer et arrêter).

## <a name="clean-up-resources"></a>Supprimer les ressources

Avec HDInsight, vos données étant stockées dans Stockage Azure ou Azure Data Lake Store, vous pouvez supprimer un cluster de manière sécurisée s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Si vous prévoyez de suivre le tutoriel suivant immédiatement, vous souhaiterez peut-être conserver le cluster.

Ouvrez le cluster dans le portail Azure, puis sélectionnez **Supprimer**.

![Supprimer le cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Supprimer le cluster HDInsight")

Vous pouvez également sélectionner le nom du groupe de ressources pour ouvrir la page du groupe de ressources, puis sélectionner **Supprimer le groupe de ressources**. En supprimant le groupe de ressources, vous supprimez le cluster HDInsight Spark et le compte de stockage par défaut.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Créer une trame de données Spark.
* Exécuter une requête SQL Spark sur la trame de données.

Passez à l’article suivant pour découvrir comment les données que vous avez enregistrées dans Spark peuvent être placées dans un outil analytique décisionnel tel que Power BI. 
> [!div class="nextstepaction"]
> [Analyser des données à l’aide d’outils décisionnels](apache-spark-use-bi-tools.md)

