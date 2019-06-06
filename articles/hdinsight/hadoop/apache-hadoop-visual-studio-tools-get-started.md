---
title: Se connecter à Apache Hadoop avec Data Lake Tools pour Visual Studio - Azure HDInsight
description: Apprenez à installer et utiliser Data Lake Tools pour Visual Studio afin de vous connecter aux clusters Apache Hadoop dans Azure HDInsight et exécuter des requêtes Hive.
keywords: outils Hadoop,requête hive,Visual Studio,Hadoop Visual Studio
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 42ef03d604caacf5ba18773b88e892237b5f0eae
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688532"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Utiliser Data Lake Tools pour Visual Studio afin de se connecter à Azure HDInsight et exécuter des requêtes Apache Hive

Découvrez comment utiliser [Microsoft Azure Data Lake et Stream Analytique Tools pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (également appelé Data Lake Tools) pour se connecter à Apache Hadoop clusters dans [Azure HDInsight](../hdinsight-hadoop-introduction.md) et envoyer des requêtes Hive.  

Pour plus d’informations sur l’utilisation de HDInsight, consultez les rubriques [Présentation de HDInsight](../hdinsight-hadoop-introduction.md) et [Prise en main de HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Pour plus d’informations sur la connexion au cluster Apache Storm, consultez [Développer des topologies C# pour Apache Storm sur HDInsight à l’aide de Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Vous pouvez utiliser Data Lake Tools pour Visual Studio pour accéder à Azure Data Lake Analytics et à HDInsight. Pour plus d’informations sur Data Lake Tools, consultez le [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce didacticiel et utiliser Data Lake Tools for Visual Studio, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight. Pour créer un cluster Azure HDInsight, consultez [Bien démarrer avec Apache Hadoop dans Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Pour exécuter des requêtes Apache Hive interactives, il vous faut un cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 ou version ultérieure).  Le [Visual Studio Community edition](https://visualstudio.microsoft.com/vs/community/) est gratuit.  Voir aussi [installer Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) et [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Il existe des variations d’interface légère avec Visual Studio 2019.

  > [!IMPORTANT]  
  > Data Lake Tools n’est plus pris en charge pour Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installation de Data Lake Tools pour Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 ou Visual Studio 2019  
  Pendant l’installation, vérifiez vous incluez au moins les charges de travail **développement Azure** ou **stockage de données et de traitement**.  

  Pour les installations existantes, à partir de la barre de menus, accédez à **outils** > **obtenir les outils et fonctionnalités...**  pour ouvrir le programme d’installation de Visual Studio.  Puis sélectionnez au moins les charges de travail **développement Azure** ou **stockage de données et de traitement**.

  ![Capture d’écran du programme d’installation de Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 et 2015  
  [Télécharger Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Choisissez la version de Data Lake Tools qui correspond à celle de Visual Studio.  

> [!NOTE]  
> Actuellement, seule la version anglaise de Data Lake Tools pour Visual Studio est disponible.

## <a name="update-data-lake-tools-for-visual-studio"></a>Mise à jour Data Lake Tools pour Visual Studio  

1. Ouvrez Visual Studio.

2. À partir de la barre de menus, accédez à **outils** > **Extensions et mises à jour...** .

3. À partir de la **Extensions et mises à jour** fenêtre, développez **mises à jour** sur la gauche.

4. Si une mise à jour est disponible, **Azure Data Lake et Stream d’outils d’analyse** s’affiche dans la fenêtre principale.  Sélectionnez **Update**.

> [!NOTE]  
> Vous ne pouvez utiliser que la version 2.3.0.0 ou ultérieure de Data Lake Tools pour vous connecter aux clusters Interactive Query et exécuter des requêtes Hive interactives.

## <a name="connect-to-azure-subscriptions"></a>Se connecter aux abonnements Azure
Vous pouvez utiliser Data Lake Tools pour Visual Studio pour vous connecter à vos clusters HDInsight, effectuer des opérations de gestion de base et exécuter des requêtes Hive.

> [!NOTE]  
> Pour plus d’informations sur la connexion à un cluster Hadoop générique, consultez [Écriture et soumission de requêtes Hive à l’aide de Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Pour vous connecter à votre abonnement Azure :

1. Ouvrez Visual Studio.

2. À partir de la barre de menus, accédez à **vue** > **Explorateur de serveurs**.

3. À partir de l’Explorateur de serveurs, avec le bouton droit **Azure**, sélectionnez **se connecter à abonnement Microsoft Azure...** , terminez le processus de connexion.

4. À partir de l’Explorateur de serveurs, une liste des clusters HDInsight existants apparaît. Si vous ne possédez aucun cluster, vous pouvez en créer un dans le portail Azure, avec Azure PowerShell ou à l’aide du Kit de développement logiciel (SDK) HDInsight. Pour plus d’informations, consultez la rubrique [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Capture d’écran de la Liste de clusters de l’explorateur de serveurs de Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Liste de clusters de l’explorateur de serveurs de Data Lake Tools pour Visual Studio")

5. Développez un cluster HDInsight. **Bases de données Hive**, un compte de stockage par défaut, les comptes de stockage liés et **journal du Service Hadoop** s’affichent. Vous pouvez développer davantage les entités.

Une fois que vous êtes connecté à votre abonnement Azure, vous pouvez effectuer les tâches suivantes.

Pour vous connecter au portail Azure à partir de Visual Studio :

1. À partir de l’Explorateur de serveurs, accédez à **Azure** > **HDInsight** et sélectionnez votre cluster.

2. Cliquez sur un cluster HDInsight, puis sélectionnez **gérer le Cluster dans le portail Azure [base]** .

Pour poser des questions ou envoyer des commentaires à partir de Visual Studio :

1. À partir de l’Explorateur de serveurs, accédez à **Azure** > **HDInsight**.

2. Avec le bouton droit **HDInsight** et sélectionnez **Forum MSDN** pour poser des questions ou **commenter** vos commentaires.

## <a name="link-a-cluster"></a>Lier un cluster
Vous pouvez lier un cluster en cliquant sur **HDInsight** puis sélectionnez **lier un HDInsight Cluster**. Entrez **Url de connexion**, **nom d’utilisateur** et **mot de passe**, cliquez sur **suivant** puis **Terminer**, le cluster doit être répertorié sous le nœud HDInsight réussie.

![Capture d’écran de Data Lake Tools pour la boîte de dialogue Visual Studio lien cluster](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Cliquez avec le bouton droit sur le cluster lié, sélectionnez **modifier**, utilisateur peut mettre à jour les informations du cluster. Ajout d’un cluster HDInsight prend en charge uniquement Hive pour l’instant.

![Capture d’écran de Data Lake Tools pour la mise à jour des clusters de lien Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorer des ressources liées
Dans l’Explorateur de serveurs, vous pouvez voir le compte de stockage par défaut et les éventuels comptes de stockage liés. Développez le compte de stockage par défaut pour afficher les conteneurs dans le compte de stockage. Le compte de stockage par défaut et le conteneur par défaut sont marqués. Cliquez avec le bouton droit sur n’importe quel conteneur pour afficher son contenu.

![Capture d’écran des Ressources liées de la liste de l’explorateur de serveurs de Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Ressources liées de la liste")

Après l’ouverture d’un conteneur, vous pouvez utiliser les boutons suivants pour charger, supprimer et télécharger des objets blob :

![Capture d’écran des Opérations blob de l’explorateur de serveurs de Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Charger, supprimer et télécharger des objets blob dans l’explorateur de serveurs")

## <a name="run-interactive-apache-hive-queries"></a>Exécuter des requêtes Apache Hive interactives
[Apache Hive](https://hive.apache.org) est une infrastructure d’entrepôt de données construite sur Hadoop. Hive est utilisée pour le résumé, les requêtes et l’analyse des données. Vous pouvez utiliser Data Lake Tools pour Visual Studio pour exécuter des requêtes Hive à partir de Visual Studio. Pour obtenir plus d’informations sur Hive, consultez [Utiliser Apache Hive avec HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) utilise [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) dans Apache Hive 2.1. Interactive Query permet l’interactivité dans des requêtes d’entrepôt de données complexes sur des jeux de données volumineux stockés. L’exécution de requêtes Hive sur Interactive Query est beaucoup plus rapide que les programmes de traitement par lots Hive traditionnels. 

> [!NOTE]  
> Vous ne pouvez exécuter des requêtes Hive interactives que lorsque vous vous connectez à un cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Vous pouvez aussi utiliser Data Lake Tools pour Visual Studio pour voir ce que contient la tâche Hive. Data Lake Tools pour Visual Studio collecte et fait apparaître les journaux d’activité Yarn de certaines tâches Hive.

À partir de l’Explorateur de serveurs, accédez à **Azure** > **HDInsight** et sélectionnez votre cluster.  Ce sera le point de départ dans l’Explorateur de serveurs pour les sections à suivre.

### <a name="view-hivesampletable"></a>Vue hivesampletable
Tous les clusters HDInsight ont une valeur par défaut exemple de table Hive appelée `hivesampletable`.  

À partir de votre cluster, accédez à **bases de données Hive** > **par défaut** > **hivesampletable**.

* Pour afficher les `hivesampletable` schéma :  
Développez **hivesampletable**.

* Pour afficher les `hivesampletable` données :  
Avec le bouton droit **hivesampletable**, puis sélectionnez **afficher les 100 premières lignes**.  Cela revient à exécuter la requête Hive suivante à l’aide du pilote ODBC Hive :

   `SELECT * FROM hivesampletable LIMIT 100`

  Vous pouvez personnaliser le nombre de lignes.

  ![Capture d’écran d’une requête de schéma Hive dans HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Résultats de la requête Hive")

### <a name="create-hive-tables"></a>Créer des tables Hive
Vous pouvez utiliser des requêtes Hive ou utiliser la GUI pour créer une table Hive. Pour plus d’informations relatives à l’utilisation de requêtes Hive, consultez [Exécuter des requêtes Apache Hive](#run.queries).

1. À partir de votre cluster, accédez à **bases de données Hive** > **par défaut**.

2. Avec le bouton droit **par défaut**, puis sélectionnez **Create Table**.

3. Comme vous le souhaitez, configurez la table.  

4. Sélectionnez **Créer une table** pour envoyer la tâche de création d’une table Hive.

    ![Capture d’écran de la fenêtre de création d’une table Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Créer une table Hive")

### <a name="run.queries"></a>Créer et exécuter des requêtes Hive
Vous pouvez créer et exécuter des requêtes Hive de deux façons :

* Création de requêtes ad hoc
* Création d’une application Hive

Pour créer et exécuter des requêtes ad hoc :

1. Cliquez sur le cluster où vous souhaitez exécuter la requête, puis sélectionnez **écrire une requête Hive**.  

2. Entrez la requête Hive suivante :

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    L’éditeur Hive prend en charge IntelliSense. Data Lake Tools pour Visual Studio prend en charge le chargement des métadonnées distantes pendant la modification d’un script Hive. Par exemple, si vous tapez `SELECT * FROM`, IntelliSense répertorie tous les noms de table suggérés. Lorsqu’un nom de table est spécifié, IntelliSense répertorie les noms de colonne. Les outils prennent en charge la plupart des instructions DML, sous-requêtes et fonctions définies par l’utilisateur intégrées de Hive.

    ![Capture d’écran de l’exemple 1 d’IntelliSense dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "IntelliSense U-SQL")

    ![Capture d’écran de l’exemple 2 d’IntelliSense dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "IntelliSense U-SQL")

   > [!NOTE]  
   > IntelliSense propose uniquement les métadonnées du cluster sélectionné dans la barre d’outils HDInsight.

3. Choisissez le mode d’exécution :

    * **Interactive**  

      Vérifiez **Interactive** est sélectionné, puis sélectionnez **Execute**.

      ![Capture d’écran de requête et exécuter](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Vérifiez **Batch** est sélectionné, puis sélectionnez **envoyer**.  Si vous sélectionnez l’option d’envoi avancé, configurez **nom de la tâche**, **Arguments**, **Configurations supplémentaires**, et **répertoire d’état**pour le script.

      ![Capture d’écran de requête et de traitement par lots](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Capture d’écran de la Requête HDInsight Hadoop Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Soumettre des requêtes")

      > [!NOTE]  
      > Vous ne pouvez pas envoyer des lots aux clusters Interactive Query.  Vous devez utiliser le mode interactif.

Pour créer et exécuter une solution Hive :

1. À partir de la barre de menus, accédez à **fichier** > **New** > **projet...** .

2. Dans le volet gauche, accédez à **installé** > **Azure Data Lake** > **HIVE (HDInsight)** .  

3. Dans le volet central, sélectionnez **Application Hive**. Entrez les propriétés, puis sélectionnez **OK**.

    ![Capture d’écran d’un nouveau projet Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Créer des applications Hive à partir de Visual Studio")

4. Dans l’**Explorateur de solutions**, double-cliquez sur le script **Script.hql** pour l’ouvrir.

### <a name="view-job-summary-and-output"></a>Afficher le résumé de tâche et de sortie

Le résumé du travail varie légèrement entre **Batch** et **Interactive** mode.

![Résumé du travail](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "résumé du travail Hive")

Utilisez le **Actualiser** bouton Mettre à jour l’état jusqu'à ce que l’état du travail devient **terminé**.  

* Pour les détails du travail à partir de **Batch** mode, sélectionnez les liens en bas pour voir **requête de tâche**, **sortie de la tâche**, **journal de la tâche**, ou **Journal yarn**.

* Pour les détails du travail à partir de **Interactive** mode, consultez onglets **sortie** et **HiveServer2 sortie**.

  ![Détails du travail](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive détails du travail")

### <a name="view-job-graph"></a>Graphique du travail

Actuellement, les graphiques de tâches sont affichées uniquement pour les travaux Hive utilisant Tez comme moteur d’exécution.  Pour plus d’informations sur l’activation de Tez, voir [Utiliser Apache Hive dans HDInsight](hdinsight-use-hive.md).  Voir aussi [utilisation d’Apache Tez au lieu de Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Pour afficher tous les opérateurs à l’intérieur d’un vertex, double-cliquez sur les vertex du graphique de la tâche. Vous pouvez aussi pointer vers un opérateur spécifique pour afficher plus d’informations sur ce dernier.

Le graphique du travail n’apparaît ne peut-être pas même si Tez est spécifié en tant que le moteur d’exécution si aucune application Tez n’est lancée.  Cela peut se produire si la tâche ne contient pas d’instructions DML, ou les instructions DML peuvent retourner sans lancer une application Tez. Par exemple, `SELECT * FROM table1` ne lancera pas l’application Tez.

![Graphique du travail](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "résumé du travail Hive")


### <a name="task-execution-detail"></a>Détail d'exécution de la tâche

Dans le graphique du travail, vous pouvez sélectionner **détail de l’exécution de la tâche** obtenir structurées et visualisées des informations sur les travaux Hive. Vous pouvez aussi obtenir plus de détails de la tâche. En cas de problèmes de performances, vous pouvez utiliser la vue pour obtenir plus d’informations sur le problème. Par exemple, vous pouvez obtenir des informations sur le fonctionnement de chaque tâche, et des informations détaillées sur chaque tâche (lecture et écriture de données, heure de planification, de début et de fin, etc). Utilisez ces informations pour ajuster les configurations de tâche ou l’architecture du système basée sur les informations affichées.

![Capture d’écran de la fenêtre Vue de l’exécution de la tâche Data Lake Tools pour Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Vue de l’exécution de la tâche")


### <a name="view-hive-jobs"></a>Afficher les tâches Hive
Vous pouvez afficher les requêtes, la sortie, le journal d’activité et le journal Yarn des tâches Hive.

Dans la version la plus récente des outils, vous pouvez consulter le contenu de vos tâches Hive en collectant et en exposant les journaux d’activité YARN. Le journal YARN peut vous aider à examiner les problèmes de performances. Pour plus d’informations sur la collection des journaux d’activité YARN par HDInsight, consultez [Accès par programme aux journaux des applications HDInsight](../hdinsight-hadoop-access-yarn-app-logs.md).

Pour afficher les tâches Hive :

1. Cliquez sur un cluster HDInsight, puis sélectionnez **afficher les tâches**. Une liste des tâches Hive exécutées sur le cluster s’affiche.  

2. Sélectionnez une tâche. Dans la fenêtre **Résumé de la tâche Hive**, sélectionnez l’un des éléments suivants :
    - **Requête de tâche**
    - **Sortie de travail**
    - **Journal de la tâche**  
    - **Journal YARN**

    ![Capture d’écran de la fenêtre d’affichage des tâches Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Afficher les tâches Hive")


## <a name="run-apache-pig-scripts"></a>Exécuter des scripts Apache Pig

1. À partir de la barre de menus, accédez à **fichier** > **New** > **projet...** .

2. Dans le volet gauche, accédez à **installé** > **Azure Data Lake** > **Pig (HDInsight)** .  

3. Dans le volet central, sélectionnez **Pig Application**. Entrez les propriétés, puis sélectionnez **OK**.

4. Dans **l’Explorateur de solutions**, double-cliquez sur **Script.pig** pour ouvrir le script.

## <a name="feedback-and-known-issues"></a>Commentaires et problèmes connus
* Un problème où les résultats démarrés avec des valeurs null ne s’affichaient pas a été résolu. Si vous êtes bloqué sur ce problème, contactez le support technique.
* Le script HQL créé par Visual Studio est encodé selon le paramètre de région locale de l’utilisateur. Le script ne s’exécute pas correctement si vous le chargez dans un cluster en tant que fichier binaire.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser le package Data Lake Tools pour Visual Studio pour vous connecter à des clusters HDInsight de Visual Studio. Vous avez aussi appris à exécuter une requête Hive. Pour plus d’informations, voir les articles suivants :

* [Exécuter des requêtes Apache Hive avec Data Lake tools pour Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Utilisation de Hadoop Hive dans HDInsight](hdinsight-use-hive.md)
* [Bien démarrer avec Apache Hadoop dans HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Envoyer des tâches Apache Hadoop dans HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analyser des données Twitter avec Apache Hadoop dans HDInsight](../hdinsight-analyze-twitter-data.md)

