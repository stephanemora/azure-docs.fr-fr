---
title: Apache Hadoop et Azure Data Lake Tools pour Visual Studio – Azure HDInsight
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
ms.openlocfilehash: 44a076ee6979e207ac3992f76d3b89cc188d53b8
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076293"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Utiliser Data Lake Tools pour Visual Studio afin de se connecter à Azure HDInsight et exécuter des requêtes Apache Hive

Apprenez à utiliser [Microsoft Azure Data Lake et Stream Analytics Tools pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504). (aussi appelés Data Lake Tools) pour vous connecter aux clusters Apache Hadoop dans [Azure HDInsight](../hdinsight-hadoop-introduction.md)et soumettre des requêtes Hive.  

Pour plus d’informations sur l’utilisation de HDInsight, consultez les rubriques [Présentation de HDInsight](../hdinsight-hadoop-introduction.md) et [Prise en main de HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Pour plus d’informations sur la connexion au cluster Apache Storm, consultez [Développer des topologies C# pour Apache Storm sur HDInsight à l’aide de Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Vous pouvez utiliser Data Lake Tools pour Visual Studio pour accéder à Azure Data Lake Analytics et à HDInsight. Pour plus d’informations sur Data Lake Tools, consultez le [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre la procédure de cet article et utiliser Data Lake Tools for Visual Studio, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight. Pour créer un cluster Azure HDInsight, consultez [Bien démarrer avec Apache Hadoop dans Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Pour exécuter des requêtes Apache Hive interactives, il vous faut un cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 ou une version ultérieure).  [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) (édition gratuite).  Voir aussi [Installer Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) et [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Il existe de légères variantes d’interface avec Visual Studio 2019.

  > [!IMPORTANT]  
  > Data Lake Tools n’est plus pris en charge pour Visual Studio 2013.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installation de Data Lake Tools pour Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 ou Visual Studio 2019  
  Lors de l’installation, assurez-vous d’inclure au moins les charges de travail **Azure development** ou **Data storage and processing**.  

  Pour les installations existantes, dans la barre de menus, accédez à **Outils** > **Obtenir outils et fonctionnalités...** pour ouvrir Visual Studio Installer.  Sélectionnez ensuite au moins Charges de travail **Azure développement** ou **Stockage et traitement des données**.

  ![Capture d’écran de Visual Studio Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-2017-installation.png)

* Visual Studio 2013 et 2015  
  [Téléchargez Data Lake Tools](https://www.microsoft.com/download/details.aspx?id=49504). Choisissez la version de Data Lake Tools qui correspond à celle de Visual Studio.  

> [!NOTE]  
> Actuellement, seule la version anglaise de Data Lake Tools pour Visual Studio est disponible.

## <a name="update-data-lake-tools-for-visual-studio"></a>Mettre à jour Azure Data Lake Tools pour Visual Studio  

1. Ouvrez Visual Studio.

2. Dans la barre de menus, accédez à **Outils** > **Extensions et mises à jour...** .

3. Dans la fenêtre **Extensions et mises à jour**, développez **Mises à jour** sur la gauche.

4. Si une mise à jour est disponible, **Azure Data Lake et Stream Analytic Tools** apparaissent dans la fenêtre principale.  Sélectionnez **Update**.

> [!NOTE]  
> Vous ne pouvez utiliser que la version 2.3.0.0 ou ultérieure de Data Lake Tools pour vous connecter aux clusters Interactive Query et exécuter des requêtes Hive interactives.

## <a name="connect-to-azure-subscriptions"></a>Se connecter aux abonnements Azure
Vous pouvez utiliser Data Lake Tools pour Visual Studio pour vous connecter à vos clusters HDInsight, effectuer des opérations de gestion de base et exécuter des requêtes Hive.

> [!NOTE]  
> Pour plus d’informations sur la connexion à un cluster Hadoop générique, consultez [Écriture et soumission de requêtes Hive à l’aide de Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx).

Pour vous connecter à votre abonnement Azure :

1. Ouvrez Visual Studio.

2. Dans la barre de menus, naviguez jusqu’à **Afficher** > **Explorateur de serveurs**.

3. À partir de l’Explorateur de serveurs, faites un clic droit sur **Azure**, sélectionnez **Se connecter à abonnement Microsoft Azure...** , puis terminez le processus de connexion.

4. Dans l’Explorateur de serveurs, une liste des clusters HDInsight existants apparaît. Si vous ne possédez aucun cluster, vous pouvez en créer un dans le portail Azure, avec Azure PowerShell ou à l’aide du Kit de développement logiciel (SDK) HDInsight. Pour plus d’informations, consultez la rubrique [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Data Lake Tools pour Visual Studio - Liste de clusters dans l’Explorateur de serveurs](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Data Lake Tools pour Visual Studio - Liste de clusters dans l’Explorateur de serveurs")

5. Développez un cluster HDInsight. Les **bases de données Hive**, un compte de stockage par défaut, les comptes de stockage liés et le **journal Hadoop Service** apparaissent. Vous pouvez développer davantage les entités.

Une fois connecté à votre abonnement Azure, vous êtes en mesure d’effectuer les tâches suivantes.

Pour vous connecter au portail Azure à partir de Visual Studio :

1. À partir de l’Explorateur de serveurs, accédez à **Azure** > **HDInsight** et sélectionnez votre cluster.

2. Cliquez avec le bouton droit sur un cluster HDInsight, puis sélectionnez **Gérer le cluster dans le Portail Microsoft Azure**.

Pour poser des questions et/ou envoyer des commentaires à partir de Visual Studio :

1. À partir de l’Explorateur de serveurs, accédez à **Azure** > **HDInsight**.

2. Faites un clic droit sur **HDInsight**, puis sélectionnez **Forum MSDN** pour poser des questions ou **Envoyer des commentaires** pour transmettre votre avis.

## <a name="link-a-cluster"></a>Lier un cluster
Vous pouvez lier un cluster en cliquant sur **HDInsight** puis en sélectionnant **Lier un cluster HDInsight**. Entrez une **URL de connexion**, un **nom d’utilisateur** et un **mot de passe**, cliquez sur **Suivant** puis sur **Terminer**, le cluster doit être répertorié sous Nœud HDInsight réussi.

![Capture d’écran de Data Lake Tools pour la boîte de dialogue Cluster de liens de Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Faites un clic droit sur le cluster lié, sélectionnez **Modifier** : l’utilisateur peut mettre à jour les informations du cluster. L’ajout d’un cluster HDInsight est uniquement possible avec Hive pour le moment.

![Capture d’écran de Data Lake Tools pour la boîte de dialogue Mise à jour du cluster de liens de Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Explorer des ressources liées
Dans l’Explorateur de serveurs, vous pouvez voir le compte de stockage par défaut et les éventuels comptes de stockage liés. Développez le compte de stockage par défaut pour afficher les conteneurs dans le compte de stockage. Le compte de stockage par défaut et le conteneur par défaut sont marqués. Cliquez avec le bouton droit sur n’importe quel conteneur pour afficher son contenu.

![Data Lake Tools pour Visual Studio - Ressources liées dans l’Explorateur de serveurs](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Liste des ressources liées")

Après l’ouverture d’un conteneur, vous pouvez utiliser les boutons suivants pour charger, supprimer et télécharger des objets blob :

![Data Lake Tools pour Visual Studio - Opérations blob dans l’Explorateur de serveurs](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Charger, supprimer et télécharger des objets blob dans l’Explorateur de serveurs")

## <a name="run-interactive-apache-hive-queries"></a>Exécuter des requêtes Apache Hive interactives
[Apache Hive](https://hive.apache.org) est une infrastructure d’entrepôt de données construite sur Hadoop. Hive est utilisée pour le résumé, les requêtes et l’analyse des données. Vous pouvez utiliser Data Lake Tools pour Visual Studio pour exécuter des requêtes Hive à partir de Visual Studio. Pour obtenir plus d’informations sur Hive, consultez [Utiliser Apache Hive avec HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) utilise [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) dans Apache Hive 2.1. Interactive Query permet l’interactivité dans des requêtes d’entrepôt de données complexes sur des jeux de données volumineux stockés. L’exécution de requêtes Hive sur Interactive Query est beaucoup plus rapide que les programmes de traitement par lots Hive traditionnels. 

> [!NOTE]  
> Vous ne pouvez exécuter des requêtes Hive interactives que lorsque vous vous connectez à un cluster [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md).

Vous pouvez aussi utiliser Data Lake Tools pour Visual Studio pour voir ce que contient la tâche Hive. Data Lake Tools pour Visual Studio collecte et fait apparaître les journaux d’activité Yarn de certaines tâches Hive.

À partir de l’Explorateur de serveurs, accédez à **Azure** > **HDInsight** et sélectionnez votre cluster.  Ce sera le point de départ dans l’Explorateur de serveurs pour les sections suivantes.

### <a name="view-hivesampletable"></a>Afficher hivesampletable
Tous les clusters HDInsight ont un exemple de tableau Hive par défaut appelé `hivesampletable`.  

Depuis votre cluster, naviguez jusqu’à **Bases de données Hive** > **par défaut** > **hivesampletable**.

* Pour visualiser le schéma `hivesampletable` :  
Développez **hivesampletable**.

* Pour afficher les données `hivesampletable` :  
Cliquez avec le bouton droit de la souris sur **hivesampletable**, et sélectionnez **Afficher les 100 premières rangées**.  Cela revient à exécuter la requête Hive suivante à l’aide du pilote ODBC Hive :

   `SELECT * FROM hivesampletable LIMIT 100`

  Vous pouvez personnaliser le nombre de lignes.

  ![Capture d’écran d’une requête de schéma Hive dans HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Résultats de la requête Hive")

### <a name="create-hive-tables"></a>Créer des tables Hive
Vous pouvez utiliser des requêtes Hive ou utiliser la GUI pour créer une table Hive. Pour plus d’informations relatives à l’utilisation de requêtes Hive, consultez [Exécuter des requêtes Apache Hive](#run.queries).

1. Depuis votre cluster, naviguez jusqu’à **Bases de données Hive** > **par défaut**.

2. Faites un clic droit sur **Par défaut**, puis sélectionnez **Créer une table**.

3. Configurez la table selon vos besoins.  

4. Sélectionnez **Créer une table** pour envoyer la tâche de création d’une table Hive.

    ![Capture d’écran de la fenêtre de création d’une table Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Créer une table Hive")

### <a name="run.queries"></a>Créer et exécuter des requêtes Hive
Vous pouvez créer et exécuter des requêtes Hive de deux façons :

* Création de requêtes ad hoc
* Création d’une application Hive

Pour créer et exécuter des requêtes ad hoc :

1. Faites un clic droit sur le cluster dans lequel vous souhaitez exécuter la requête, puis sélectionnez **Écrire une requête Hive**.  

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

      Assurez-vous que **Interactif** est sélectionné, puis sélectionnez **Exécuter**.

      ![Capture d’écran de la requête et de l’exécution](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

      Assurez-vous que **Lot** est sélectionné, puis sélectionnez **Envoyer**.  Si vous sélectionnez l’option d’envoi avancé, configurez les éléments **Nom de la tâche**, **Arguments**, **Configurations supplémentaires** et **Répertoire d’état** pour le script.

      ![Options de requête et de lot dans Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)  

      ![Capture d’écran de la Requête HDInsight Hadoop Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Soumettre des requêtes")

      > [!NOTE]  
      > Vous ne pouvez pas soumettre de lots aux groupes de requêtes interactives.  Vous devez utiliser le mode interactif.

Pour créer et exécuter une solution Hive :

1. À partir de la barre de menus, accédez à **Fichier** > **Nouveau** > **Projet...** .

2. Dans le volet gauche, accédez à **Installé** > **Azure Data Lake** > **HIVE (HDInsight)** .  

3. Dans le volet central, sélectionnez **Application Hive**. Entrez les propriétés, puis sélectionnez **OK**.

    ![Capture d’écran d’un nouveau projet Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Créer des applications Hive à partir de Visual Studio")

4. Dans l’**Explorateur de solutions**, double-cliquez sur le script **Script.hql** pour l’ouvrir.

### <a name="view-job-summary-and-output"></a>Afficher le résumé et le résultat du travail

Le résumé du travail varie légèrement entre les modes **Lot** et **Interactif**.

![Apache Hive - Onglet de présentation du résumé du travail ](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png "Résumé du travail Hive")

Utilisez le bouton **Actualiser** pour mettre à jour l’état jusqu’à ce qu’il passe à **Terminé**.  

* Pour les détails du travail en mode **Lot**, sélectionnez les liens en bas pour voir **Requête**, **Sortie du travail**, **Journal du travail**, ou **Journal Yarn**.

* Pour les détails du travail en mode **Interactif**, consultez les onglets **Sortie** et **Sortie HiveServer2**.

  ![Visual Studio - Détails du travail Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png "Détails du travail Hive")

### <a name="view-job-graph"></a>Afficher le graphique du travail

Actuellement, les graphiques des tâches ne sont affichés que pour les travaux Hive qui utilisent Tez comme moteur d’exécution.  Pour plus d’informations sur l’activation de Tez, voir [Utiliser Apache Hive dans HDInsight](hdinsight-use-hive.md).  Voir aussi, [Utiliser Apache Tez au lieu de Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Pour afficher tous les opérateurs à l’intérieur d’un vertex, double-cliquez sur les vertex du graphique de la tâche. Vous pouvez aussi pointer vers un opérateur spécifique pour afficher plus d’informations sur ce dernier.

Il est possible que le graphique du travail n’apparaisse pas, même si Tez est spécifié comme moteur d’exécution si aucune application Tez n’est lancée.  Cela peut se produire parce que le travail ne contient pas d’instructions DML, ou parce que les instructions DML peuvent revenir sans lancer une application Tez. Par exemple, `SELECT * FROM table1` ne lancera pas l’application Tez.

![Visual Studio - Graphe du travail Apache Hive](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Résumé du travail Hive")

### <a name="task-execution-detail"></a>Détail de l’exécution de la tâche

Dans le graphique du travail, vous pouvez sélectionner **Détail de l’exécution de la tâche** pour obtenir des informations structurées et visuelles sur les travaux Hive. Vous pouvez aussi obtenir plus de détails de la tâche. En cas de problèmes de performances, vous pouvez utiliser la vue pour obtenir plus d’informations sur le problème. Par exemple, vous pouvez obtenir des informations sur le fonctionnement de chaque tâche, et des informations détaillées sur chaque tâche (lecture et écriture de données, heure de planification, de début et de fin, etc). Utilisez ces informations pour ajuster les configurations de tâche ou l’architecture du système basée sur les informations affichées.

![Data Lake - Visual Studio Tools, fenêtre de la vue de l’exécution de la tâche ](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Vue de l’exécution de la tâche")


### <a name="view-hive-jobs"></a>Afficher les tâches Hive
Vous pouvez afficher les requêtes, la sortie, le journal d’activité et le journal Yarn des tâches Hive.

Dans la version la plus récente des outils, vous pouvez consulter le contenu de vos tâches Hive en collectant et en exposant les journaux d’activité YARN. Le journal YARN peut vous aider à examiner les problèmes de performances. Pour plus d’informations sur la collection des journaux d’activité YARN par HDInsight, consultez [Accès par programme aux journaux des applications HDInsight](../hdinsight-hadoop-access-yarn-app-logs.md).

Pour afficher les tâches Hive :

1. Cliquez avec le bouton droit sur un cluster HDInsight, puis sélectionnez **Afficher les travaux**. Une liste des tâches Hive exécutées sur le cluster s’affiche.  

2. Sélectionnez une tâche. Dans la fenêtre **Résumé de la tâche Hive**, sélectionnez l’un des éléments suivants :
    - **Requête de tâche**
    - **Sortie de travail**
    - **Journal de la tâche**  
    - **Journal YARN**

    ![Capture d’écran de la fenêtre d’affichage des tâches Hive dans HDInsight Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Afficher les tâches Hive")


## <a name="run-apache-pig-scripts"></a>Exécuter des scripts Apache Pig

1. À partir de la barre de menus, accédez à **Fichier** > **Nouveau** > **Projet...** .

2. Dans le volet gauche, accédez à **Installé** > **Azure Data Lake** > **Pig (HDInsight)** .  

3. Dans le volet central, sélectionnez **Application Pig**. Entrez les propriétés, puis sélectionnez **OK**.

4. Dans l’**Explorateur de solutions**, double-cliquez sur le script **Script.pig** pour l’ouvrir.

## <a name="feedback-and-known-issues"></a>Commentaires et problèmes connus
* Un problème où les résultats démarrés avec des valeurs null ne s’affichaient pas a été résolu. Si vous êtes bloqué sur ce problème, contactez le support technique.
* Le script HQL créé par Visual Studio est encodé selon le paramètre de région locale de l’utilisateur. Le script ne s’exécute pas correctement si vous le chargez dans un cluster en tant que fichier binaire.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser le package Data Lake Tools pour Visual Studio pour vous connecter à des clusters HDInsight de Visual Studio. Vous avez aussi appris à exécuter une requête Hive. Pour plus d’informations, voir les articles suivants :

* [Exécution de requêtes Apache Hive à l’aide des outils Data Lake pour Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Utilisation de Hadoop Hive dans HDInsight](hdinsight-use-hive.md)
* [Bien démarrer avec Apache Hadoop dans HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Envoyer des tâches Apache Hadoop dans HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analyser des données Twitter avec Apache Hadoop dans HDInsight](../hdinsight-analyze-twitter-data.md)

