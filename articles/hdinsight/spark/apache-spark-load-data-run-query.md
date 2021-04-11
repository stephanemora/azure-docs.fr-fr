---
title: 'Tutoriel : Charger des données et exécuter des requêtes avec Apache Spark - Azure HDInsight'
description: Didacticiel - Découvrez comment charger des données et exécuter des requêtes interactives sur des clusters Spark dans Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: eee93d338a106414235b04380e5a67c772951188
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062741"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Tutoriel : Charger des données et exécuter des requêtes sur un cluster Apache Spark dans Azure HDInsight

Dans ce tutoriel, vous allez apprendre à créer une trame de données à partir d’un fichier CSV et à exécuter des requêtes interactives SQL Spark sur un cluster [Apache Spark](https://spark.apache.org/) dans Azure HDInsight. Dans Spark, une trame de données est une collection distribuée de données organisées en colonnes nommées. D’un point de vue conceptuel, une trame de données équivaut à une table d’une base de données relationnelle ou à une trame de données dans R/Python.

Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Créer une trame de données à partir d’un fichier CSV
> * Exécuter des requêtes sur la trame de données

## <a name="prerequisites"></a>Prérequis

Un cluster Apache Spark sur HDInsight. Consultez [Créer un cluster Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Créer un bloc-notes Jupyter Notebook

Jupyter Notebook est un environnement de Notebook interactif qui prend en charge plusieurs langages de programmation. Le Notebook vous permet d’interagir avec vos données, de combiner du code avec le texte Markdown et d’effectuer des visualisations simples.

1. Modifiez l'URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` en remplaçant `SPARKCLUSTER` par le nom de votre cluster Spark. Entrez ensuite l'URL modifiée dans un navigateur web. À l’invite (le cas échéant), entrez les informations d’identification du cluster.

2. Sur la page web de Jupyter, sélectionnez **Nouveau** > **PySpark** pour créer un notebook.

   :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Créer un Jupyter Notebook pour exécuter une requête Spark SQL interactive" border="true":::

   Un nouveau notebook est créé et ouvert sous le nom Untitled(`Untitled.ipynb`).

    > [!NOTE]  
    > Lorsque vous utilisez le noyau PySpark pour créer un bloc-notes, la session `spark` est automatiquement créée lorsque vous exécutez la première cellule de code. Vous n’avez pas besoin de créer explicitement la session.

## <a name="create-a-dataframe-from-a-csv-file"></a>Créer une trame de données à partir d’un fichier CSV

Les applications peuvent créer des dataframes directement à partir de fichiers ou de dossiers dans le stockage distant, par exemple le stockage Azure ou Azure Data Lake Storage, à partir d’une table Hive ou d’autres sources de données prises en charge par Spark, telles que Cosmos DB, Azure SQL DB, DW, etc. La capture d’écran suivante montre un instantané du fichier HVAC.csv utilisé dans ce didacticiel. Le fichier CSV est fourni avec tous les clusters HDInsight Spark. Les données capturent les variations de température de certains bâtiments.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png " alt-text="Capture instantanée des données pour la requête Spark SQL interactive" border="true":::

1. Collez l’exemple de code suivant dans une cellule vide du notebook Jupyter, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. Le code importe les types requis pour ce scénario :

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Lorsque vous exécutez une requête interactive dans Jupyter, la fenêtre du navigateur web ou la légende d’onglet affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png " alt-text="État de la requête Spark SQL interactive" border="true":::

1. Notez l’ID de session retourné. D’après l’image ci-dessus, l’ID de session est 0. Si vous le souhaitez, vous pouvez récupérer les détails de la session en accédant à `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements`, où CLUSTERNAME est le nom de votre cluster Spark et ID est votre numéro d’ID de session.

1. Exécutez le code suivant pour créer une trame de données et une table temporaire (**hvac**).

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

     :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png " alt-text="Résultat de la requête Spark interactive sous forme de tableau" border="true":::

2. Vous pouvez également voir les résultats dans d’autres visualisations. Pour afficher un graphique en aires pour le même résultat, sélectionnez **Area** (Aires), puis définissez d’autres valeurs comme indiqué.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png " alt-text="Résultat de la requête Spark interactive sous forme de graphique en aires" border="true":::

3. Dans la barre de menus du notebook, accédez à **File** (Fichier) > **Save and Checkpoint**(Enregistrer et créer un point de contrôle).

4. Si vous démarrez le [didacticiel suivant](apache-spark-use-bi-tools.md) maintenant, laissez le bloc-notes ouvert. Sinon, arrêtez le notebook pour libérer les ressources de cluster : dans la barre de menus, accédez à **File** (Fichier) >  **Close and Halt** (Fermer et arrêter).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Avec HDInsight, vos données et notebooks Jupyter sont stockés dans le stockage Azure ou dans Azure Data Lake Storage, ce qui vous permet de supprimer un cluster sans risque quand celui-ci n’est pas utilisé. Vous devez également payer pour un cluster HDInsight, même quand vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, mieux vaut supprimer les clusters quand ils ne sont pas utilisés. Si vous prévoyez de suivre le tutoriel suivant immédiatement, vous souhaiterez peut-être conserver le cluster.

Ouvrez le cluster dans le portail Azure, puis sélectionnez **Supprimer**.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png " alt-text="Supprimer un cluster HDInsight" border="true":::

Vous pouvez également sélectionner le nom du groupe de ressources pour ouvrir la page du groupe de ressources, puis sélectionner **Supprimer le groupe de ressources**. En supprimant le groupe de ressources, vous supprimez le cluster HDInsight Spark et le compte de stockage par défaut.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à créer une trame de données à partir d'un fichier CSV et à exécuter des requêtes SQL Spark interactives sur un cluster Apache Spark dans Azure HDInsight. Passez à l’article suivant pour découvrir comment les données que vous avez inscrites dans Apache Spark peuvent être tirées (pull) et placées dans un outil analytique décisionnel tel que Power BI.

> [!div class="nextstepaction"]
> [Analyser des données à l’aide d’outils décisionnels](apache-spark-use-bi-tools.md)
