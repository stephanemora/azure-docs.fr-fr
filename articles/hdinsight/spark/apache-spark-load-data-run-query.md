---
title: 'Didacticiel : Charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight '
description: Découvrez comment charger des données et exécuter des requêtes interactives sur des clusters Spark dans Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 04/03/2019
ms.openlocfilehash: f480aeb7e126cb6ab8286bbfbfb8441fefeb07ef
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64716087"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Didacticiel : Charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight

Dans ce tutoriel, vous allez apprendre à créer une trame de données à partir d’un fichier CSV et à exécuter des requêtes interactives SQL Spark sur un cluster [Apache Spark](https://spark.apache.org/) dans Azure HDInsight. Dans Spark, une trame de données est une collection distribuée de données organisées en colonnes nommées. D’un point de vue conceptuel, une trame de données équivaut à une table d’une base de données relationnelle ou à une trame de données dans R/Python.
 
Ce tutoriel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer une trame de données à partir d’un fichier CSV
> * Exécuter des requêtes sur la trame de données

## <a name="prerequisites"></a>Prérequis

* Effectuez la procédure décrite dans [Créer un cluster Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-dataframe-from-a-csv-file"></a>Créer une trame de données à partir d’un fichier CSV

Les applications peuvent créer des tableaux de données directement à partir de fichiers ou de dossiers dans le stockage à distance, tels que Stockage Azure ou Azure Data Lake Storage; à partir d’une table Hive, ou d’autres sources de données prises en charge par Spark, telles que Cosmos DB, la base de données SQL Azure, DW, etc. La capture d’écran suivante montre un instantané du fichier HVAC.csv utilisé dans ce didacticiel. Le fichier CSV est fourni avec tous les clusters HDInsight Spark. Les données capturent les variations de température de certains bâtiments.
    
![Instantané des données pour les requêtes Spark SQL interactives](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "instantané des données pour les requêtes Spark SQL interactives")


1. Ouvrez le notebook Jupyter que vous avez créé dans la section des prérequis, puis créez un notebook avec PySpark.

    > [!NOTE]  
    > Lorsque vous utilisez le noyau PySpark pour créer un bloc-notes, la session `spark` est automatiquement créée lorsque vous exécutez la première cellule de code. Vous n’avez pas besoin de créer explicitement la session.

2. Collez l’exemple de code suivant dans une cellule vide du bloc-notes, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. Le code importe les types requis pour ce scénario :

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Lorsque vous exécutez une requête interactive dans Jupyter, la fenêtre du navigateur web ou la légende d’onglet affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

    ![État de la requête interactive Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "État de la requête interactive Spark SQL")

3. Exécutez le code suivant pour créer une trame de données et une table temporaire (**hvac**). 

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Exécuter des requêtes sur la trame de données

Une fois la table créée, vous pouvez exécuter une requête interactive sur les données.

1. Exécutez le code suivant dans une cellule vide du bloc-notes :

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   La sortie sous forme de tableau suivante s’affiche.

     ![Sortie sous forme de tableau d’un résultat de requête interactive Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Sortie sous forme de tableau d’un résultat de requête interactive Spark")

2. Vous pouvez également voir les résultats dans d’autres visualisations. Pour afficher un graphique en aires pour le même résultat, sélectionnez **Area** (Aires), puis définissez d’autres valeurs comme indiqué.

    ![Résultat de requête interactive Spark sous forme graphique](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Résultat de requête interactive Spark sous forme graphique")

3. Dans la barre de menus du notebook, accédez à **File** (Fichier) > **Save and Checkpoint**(Enregistrer et créer un point de contrôle).

4. Si vous démarrez le [didacticiel suivant](apache-spark-use-bi-tools.md) maintenant, laissez le bloc-notes ouvert. Sinon, arrêtez le notebook pour libérer les ressources de cluster : dans la barre de menus, accédez à **File** (Fichier) >  **Close and Halt** (Fermer et arrêter).

## <a name="clean-up-resources"></a>Supprimer des ressources

Avec HDInsight, vos données et blocs-notes Jupyter sont stockés dans Stockage Azure ou Azure Data Lake Storage. Vous pouvez ainsi supprimer un cluster de manière sécurisée s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Si vous prévoyez de suivre le tutoriel suivant immédiatement, vous souhaiterez peut-être conserver le cluster.

Ouvrez le cluster dans le portail Azure, puis sélectionnez **Supprimer**.

![Supprimer le cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Supprimer le cluster HDInsight")

Vous pouvez également sélectionner le nom du groupe de ressources pour ouvrir la page du groupe de ressources, puis sélectionner **Supprimer le groupe de ressources**. En supprimant le groupe de ressources, vous supprimez le cluster HDInsight Spark et le compte de stockage par défaut.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :
> [!div class="checklist"]
> * Créez une trame de données Apache Spark.
> * Exécuter une requête SQL Spark sur la trame de données.

Passez à l’article suivant pour découvrir comment les données que vous avez inscrites dans Apache Spark peuvent être tirées (pull) et placées dans un outil analytique décisionnel tel que Power BI. 
> [!div class="nextstepaction"]
> [Analyser des données à l’aide d’outils décisionnels](apache-spark-use-bi-tools.md)
