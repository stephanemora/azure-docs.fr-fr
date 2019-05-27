---
title: Apache Hive avec les outils Data Lake (Apache Hadoop) pour Visual Studio - Azure HDInsight
description: Découvrez comment utiliser les outils Data Lake pour Visual Studio pour exécuter des requêtes Apache Hive avec Apache Hadoop sur Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861594"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Exécution de requêtes Apache Hive à l’aide des outils Data Lake pour Visual Studio

Découvrez comment utiliser les outils Data Lake pour Visual Studio pour interroger Apache Hive. Les outils Data Lake vous permettent de facilement créer, soumettre et surveiller les requêtes Hive à Apache Hadoop sur Azure HDInsight.

## <a id="prereq"></a>Configuration requise

* Un cluster Apache Hadoop sur HDInsight. Consultez [prise en main HDInsight sous Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (l'une des versions suivantes) :

    * Visual Studio 2015, 2017 (toute édition)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate avec mise à jour 4

* Outils HDInsight pour Visual Studio ou Outils Azure Data Lake pour Visual Studio. Consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](apache-hadoop-visual-studio-tools-get-started.md) pour connaître les étapes d’installation et de configuration des outils.

## <a id="run"></a> Exécuter des requêtes Apache Hive avec Visual Studio

Vous pouvez créer et exécuter des requêtes Hive de deux façons :

* Créer des requêtes ad hoc
* Création d’une application Hive

### <a name="ad-hoc"></a>Ad hoc

Requêtes ad hoc peuvent être exécutées dans le **Batch** ou **Interactive** mode.

1. Ouvrez **Visual Studio**.

2. À partir de **Explorateur de serveurs**, accédez à **Azure** > **HDInsight**.

3. Développez **HDInsight**et cliquez sur le cluster où vous souhaitez exécuter la requête, puis sélectionnez **écrire une requête Hive**.

4. Entrez la requête hive suivante :

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Sélectionnez **Exécuter**. Notez que le mode d’exécution est défini par défaut pour **Interactive**.

    ![Capture d’écran d’exécution de requêtes Hive interactives](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Pour exécuter la même requête **Batch** mode, la liste d’activer/désactiver la liste déroulante à partir de **Interactive** à **Batch**. Notez que le bouton d’exécution passe de **Execute** à **envoyer**.

    ![Capture d’écran de soumission d’une requête Hive](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    L’éditeur Hive prend en charge IntelliSense. Data Lake Tools pour Visual Studio prend en charge le chargement des métadonnées distantes pendant la modification d’un script Hive. Par exemple, si vous tapez `SELECT * FROM`, IntelliSense répertorie tous les noms de table suggérés. Lorsqu’un nom de table est spécifié, IntelliSense répertorie les noms de colonne. Les outils prennent en charge la plupart des instructions DML, sous-requêtes et fonctions définies par l’utilisateur intégrées de Hive. IntelliSense propose uniquement les métadonnées du cluster sélectionné dans la barre d’outils HDInsight.

    ![Capture d’écran de l’exemple 1 d’IntelliSense dans HDInsight Visual Studio Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "IntelliSense U-SQL")
   
    ![Capture d’écran de l’exemple 2 d’IntelliSense dans HDInsight Visual Studio Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "IntelliSense U-SQL")

7. Sélectionnez **Envoyer** ou **Envoyer (Avancé)**.

   Si vous sélectionnez l’option d’envoi avancé, configurez les éléments **Nom de la tâche**, **Arguments**, **Configurations supplémentaires** et **Répertoire d’état** pour le script :

    ![Capture d’écran de la Requête HDInsight Hadoop Hive](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Soumettre des requêtes")

### <a name="hive-application"></a>Application de la ruche

1. Ouvrez **Visual Studio**.

2. À partir de la barre de menus, accédez à **fichier** > **New** > **projet**.

3. À partir de la **nouveau projet** fenêtre, accédez à **modèles** > **Azure Data Lake** > **HIVE (HDInsight)**  >  **Hive Application**. 

4. Fournissez un nom pour ce projet, puis sélectionnez **OK**.

5. Ouvrez le fichier **Script.hql** créé avec ce projet et collez les instructions HiveQL suivantes :

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Ces instructions effectuent les opérations suivantes :

   * `DROP TABLE`: Si la table existe, cette instruction la supprime.

   * `CREATE EXTERNAL TABLE`: Crée une table « externe » dans Hive. Les tables externes stockent uniquement la définition de table dans Hive (les données restent à leur emplacement d’origine).

     > [!NOTE]  
     > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, Par exemple, un travail MapReduce ou service Azure.
     >
     > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.

   * `ROW FORMAT`: Indique à Hive la façon dont les données sont mises en forme. Dans ce cas, les champs de chaque journal sont séparés par un espace.

   * `STORED AS TEXTFILE LOCATION`: Indique à Hive que des données sont stockées dans le répertoire example/data sous forme de texte.

   * `SELECT`: Sélectionne toutes les lignes dont la colonne `t4` contient la valeur `[ERROR]`. Cette instruction renvoie la valeur `3`, car trois lignes contiennent cette valeur.

   * `INPUT__FILE__NAME LIKE '%.log'` : indique à Hive de retourner uniquement des données provenant de fichiers se terminant par .log. Cette clause limite la recherche au fichier sample.log qui contient les données.

6. Dans la barre d’outils, sélectionnez le **cluster HDInsight** que vous souhaitez utiliser pour cette requête. Sélectionnez **Envoyer** pour exécuter les instructions en tant que tâche Hive.

   ![Barre d’envoi](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. Le **résumé de tâche Hive** apparaît et affiche des informations sur la tâche en cours d’exécution. Utilisez le lien **Actualiser** pour actualiser les informations sur la tâche, jusqu’à ce que l’**état de la tâche** passe à **Terminé**.

   ![résumé de tâche affichant un travail terminé](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Utilisez le lien **Sortie de la tâche** pour afficher la sortie de cette tâche. Il affiche `[ERROR] 3`, soit la valeur retournée par cette requête.

### <a name="additional-example"></a>Exemple supplémentaire

Cet exemple s’appuie sur le `log4jLogs` table créée à l’étape précédente.

1. À partir de **Explorateur de serveurs**, avec le bouton droit de votre cluster et sélectionnez **écrire une requête Hive**.

2. Entrez la requête hive suivante :

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Ces instructions effectuent les opérations suivantes :

    * `CREATE TABLE IF NOT EXISTS`: Crée une table, si elle n’existe pas déjà. Étant donné que le mot-clé `EXTERNAL` n’est pas utilisé, cette instruction crée une table interne. Les tables internes sont stockées dans l’entrepôt de données Hive et gérées par Hive.
    
    > [!NOTE]  
    > Contrairement aux tables `EXTERNAL`, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

    * `STORED AS ORC`: Stocke les données dans un format ORC (Optimized Row Columnar). ORC est un format particulièrement efficace et optimisé pour le stockage de données Hive.
    
    * `INSERT OVERWRITE ... SELECT`: Sélectionne des lignes de la table `log4jLogs` qui contiennent `[ERROR]`, puis insère les données dans la table `errorLogs`.

3. Exécutez la requête dans **Batch** mode.

4. Pour vérifier que le travail a créé une table, utilisez **Explorateur de serveurs** et développez **Azure** > **HDInsight** > votre cluster HDInsight > **Bases de données Hive** > **par défaut**. La table **errorLogs** et la table **log4jLogs** sont répertoriées.

## <a id="nextsteps"></a>Étapes suivantes

Comme vous pouvez le voir, les outils HDInsight pour Visual Studio fournissent un moyen simple de travailler avec des requêtes Hive sur HDInsight.

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les outils de HDInsight pour Visual Studio :

* [Prise en main des outils HDInsight pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)