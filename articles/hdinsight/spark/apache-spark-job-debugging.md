---
title: Déboguer des travaux Apache Spark en cours d’exécution sur Azure HDInsight
description: Utilisez l’interface utilisateur YARN, l’interface utilisateur Spark et le serveur d’historique Spark pour suivre et déboguer les tâches en cours d’exécution sur un cluster Spark dans Azure HDInsight
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: hrasheed
ms.openlocfilehash: 6afb54caca572988c566ab7c6325d511e77fbd3e
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582086"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Déboguer des travaux Apache Spark en cours d’exécution sur Azure HDInsight

Cet article explique comment suivre et déboguer des tâches [Apache Spark](https://spark.apache.org/) en cours d’exécution sur des clusters HDInsight avec l’interface utilisateur [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), l’interface utilisateur Spark et le serveur d’historique Spark. Vous démarrez une tâche Spark à partir d’un bloc-notes disponible avec le cluster Spark, **Machine Learning : analyse prédictive des données d’inspections alimentaires à l’aide de MLLib**. Vous pouvez utiliser les étapes ci-dessous pour effectuer le suivi d’une application que vous avez envoyée avec une autre méthode, par exemple, **spark-submit**.

## <a name="prerequisites"></a>Prérequis
Vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Vous devez normalement avoir commencé à exécuter le bloc-notes, **[Machine Learning : analyse prédictive des données d’inspections alimentaires à l’aide de MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Pour obtenir des instructions sur l’exécution de ce bloc-notes, suivez le lien.  

## <a name="track-an-application-in-the-yarn-ui"></a>Effectuer le suivi d’une application dans l’interface utilisateur YARN
1. Lancez l’interface utilisateur YARN. Cliquez sur **Tableaux de bord du cluster**, puis sur **YARN**.
   
    ![Lancer l’interface utilisateur Yarn](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Vous pouvez également lancer l’interface utilisateur de YARN à partir de celle d’Ambari. Pour lancer l’interface utilisateur d’Ambari, cliquez sur **Tableau de bord du cluster**, puis sur **Tableau de bord de cluster HDInsight**. À partir de l’interface utilisateur d’Ambari, cliquez successivement sur **YARN**, **Quick Links** (Liens rapides), le Gestionnaire des ressources actif et **ResourceManager UI** (IU de ResourceManager).    
   > 
   > 
2. Étant donné que vous avez démarré le travail Spark à l’aide des blocs-notes Jupyter, l’application porte le nom **remotesparkmagics** (nom de toutes les applications démarrées à partir du bloc-notes). Cliquez sur l’ID d’application en regard du nom de l’application pour obtenir plus d’informations sur le travail. Cette action lance la vue de l’application.
   
    ![Rechercher l’ID d’application Spark](./media/apache-spark-job-debugging/find-application-id.png)
   
    Pour les applications lancées à partir des bloc-notes Jupyter, l’état est toujours **EN COURS D’EXÉCUTION** tant que vous ne fermez pas le bloc-notes.
3. Dans la vue de l’application, vous pouvez descendre pour rechercher les conteneurs associés à l’application et aux journaux (stdout/stderr). Vous pouvez également lancer l’interface utilisateur Spark en cliquant sur le lien qui correspond à l’ **URL de suivi**, comme indiqué ci-dessous. 
   
    ![Télécharger les journaux de conteneur](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Effectuer le suivi d’une application dans l’interface utilisateur Spark
Dans l’interface utilisateur Spark, vous pouvez explorer les travaux Spark générés par l’application que vous avez démarrée précédemment.

1. Pour lancer l’interface utilisateur Spark, dans la vue de l’application, cliquez sur le lien **URL de suivi**, comme illustré dans la capture d’écran ci-dessus. Vous pouvez y voir tous les travaux Spark lancés par l’application en cours d’exécution dans le bloc-notes Jupyter.
   
    ![Afficher les travaux Spark](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Cliquez sur l’onglet **Exécuteurs** pour consulter les informations de traitement et de stockage pour chaque exécuteur. Vous pouvez également récupérer la pile des appels en cliquant sur le lien **Thread Dump** .
   
    ![Afficher les exécuteurs Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Cliquez sur l’onglet **Étapes** pour consulter les étapes de l’application.
   
    ![Afficher les étapes Spark](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Chaque étape peut comporter plusieurs tâches dont vous pouvez afficher les statistiques d’exécution, comme illustré ci-dessous.
   
    ![Afficher les étapes Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Dans la page de détails de l’étape, vous pouvez lancer la visualisation DAG. Développez le lien **DAG Visualization** (Visualisation DAG) situé en haut de la page, comme indiqué ci-dessous.
   
    ![Afficher la visualisation DAG des étapes Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    Le graphique DAG (Direct Aclyic Graph) représente les différentes étapes de l’application. Chaque rectangle bleu dans le graphique représente une opération Spark appelée à partir de l’application.
5. Dans la page de détails de l’étape, vous pouvez également lancer la vue Chronologie de l’application. Développez le lien **Event Timeline** (Chronologie de l’événement) situé en haut de la page, comme indiqué ci-dessous.
   
    ![Afficher la chronologie d’événement des étapes Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Vous obtenez les événements Spark sous la forme d’une chronologie. La vue chronologie est disponible sur trois niveaux : entre différents travaux, dans un travail et dans une étape. L’image ci-dessus capture la vue chronologie pour une étape donnée.
   
   > [!TIP]
   > Si vous cochez la case **Activer le zoom** , vous pouvez faire défiler la vue chronologie vers la gauche et vers la droite.
   > 
   > 
6. Les autres onglets de l’interface utilisateur Spark fournissent également des informations utiles sur l’instance Spark.
   
   * Onglet Stockage : si votre application crée des RDD, vous trouverez des informations à ce sujet sous l’onglet Stockage.
   * Onglet Environnement : cet onglet fournit de nombreuses informations utiles concernant votre instance Spark, par exemple : 
     * version de Scala ;
     * répertoire du journal des événements associé au cluster ;
     * nombre de cœurs d’exécuteur de l’application ;
     * etc.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Rechercher des informations sur les tâches terminées à l’aide du serveur d’historique Spark
Une fois qu’un travail est terminé, les informations concernant ce travail sont conservées dans le serveur d’historique Spark.

1. Pour lancer le serveur d’historique Spark, dans le panneau du cluster, cliquez sur **Tableau de bord du cluster**, puis cliquez sur **Serveur d’historique Spark**.
   
    ![Lancer le serveur d’historique Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Vous pouvez également lancer l’interface utilisateur du serveur d’historique Spark à partir de celle d’Ambari. Pour lancer l’interface utilisateur d’Ambari, dans le panneau du cluster, cliquez sur **Tableau de bord du cluster**, puis sur **Tableau de bord de cluster HDInsight**. À partir de l’interface utilisateur Ambari, cliquez sur **Spark**, **Quick Links** (Liens rapides), puis cliquez sur **Spark History Server UI** (Interface utilisateur du serveur d’historique Spark).
   > 
   > 
2. Les applications terminées s’affichent dans une liste. Cliquez sur un ID d’application pour obtenir plus d’informations sur l’application.
   
    ![Lancer le serveur d’historique Spark](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Voir aussi
*  [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
*  [Déboguer des tâches Apache Spark avec le serveur d’historique Spark étendu](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Pour les analystes de données

* [Apache Spark avec Machine Learning : Utiliser Spark dans HDInsight pour analyser la température d’un bâtiment à l’aide de données issues des systèmes de chauffage, de ventilation et de climatisation](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats d’une inspection alimentaire](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web avec Apache Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analyse de données de télémétrie Application Insight avec Spark dans HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Utiliser Caffe sur Azure HDInsight Spark pour une formation approfondie échelonnée](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Pour les développeurs Spark

* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance sur un cluster Apache Spark avec Apache Livy](apache-spark-livy-rest-interface.md)
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utiliser le plug-in Azure HDInsight Tools pour IntelliJ IDEA afin de déboguer des applications Apache Spark à distance](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des blocs-notes Apache Zeppelin avec un cluster Apache Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux accessibles à Jupyter Notebook dans le cluster Apache Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)


