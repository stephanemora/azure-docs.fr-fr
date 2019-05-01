---
title: Apache Hive avec les outils Data Lake (Apache Hadoop) pour Visual Studio - Azure HDInsight
description: Découvrez comment utiliser les outils Data Lake pour Visual Studio pour exécuter des requêtes Apache Hive avec Apache Hadoop sur Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 3a2e81234702e1fcff0349a14a4bc2852d257ad6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686169"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Exécution de requêtes Apache Hive à l’aide des outils Data Lake pour Visual Studio

Découvrez comment utiliser les outils Data Lake pour Visual Studio pour interroger Apache Hive. Les outils Data Lake vous permettent de facilement créer, soumettre et surveiller les requêtes Hive à Apache Hadoop sur Azure HDInsight.

## <a id="prereq"></a>Configuration requise

* Un cluster Azure HDInsight (Apache Hadoop sur HDInsight)

  > [!IMPORTANT]  
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (l'une des versions suivantes) :

    * Visual Studio 2013 Community/Professional/Premium/Ultimate avec mise à jour 4

    * Visual Studio 2015 (toute édition)

    * Visual Studio 2017 (toute édition)

* Outils HDInsight pour Visual Studio ou Outils Azure Data Lake pour Visual Studio. Consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](apache-hadoop-visual-studio-tools-get-started.md) pour connaître les étapes d’installation et de configuration des outils.

## <a id="run"></a> Exécuter des requêtes Apache Hive avec Visual Studio

1. Ouvrez **Visual Studio** et sélectionnez **Nouveau** > **Projet** > **Azure Data Lake** > **HIVE** > **Application Hive**. Fournissez un nom pour ce projet.

2. Ouvrez le fichier **Script.hql** créé avec ce projet et collez les instructions HiveQL suivantes :

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

3. Dans la barre d’outils, sélectionnez le **cluster HDInsight** que vous souhaitez utiliser pour cette requête. Sélectionnez **Envoyer** pour exécuter les instructions en tant que tâche Hive.

   ![Barre d’envoi](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. Le **résumé de tâche Hive** apparaît et affiche des informations sur la tâche en cours d’exécution. Utilisez le lien **Actualiser** pour actualiser les informations sur la tâche, jusqu’à ce que l’**état de la tâche** passe à **Terminé**.

   ![résumé de tâche affichant un travail terminé](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Utilisez le lien **Sortie de la tâche** pour afficher la sortie de cette tâche. Il affiche `[ERROR] 3`, soit la valeur retournée par cette requête.

6. Vous pouvez également exécuter des requêtes Hive sans créer de projet. À l’aide de l’**Explorateur de serveurs**, développez **Azure** > **HDInsight**, cliquez avec le bouton droit sur votre serveur HDInsight, puis sélectionnez **Écrire une requête Hive**.

7. Dans le document **temp.hql** qui s’affiche, ajoutez les instructions HiveQL suivantes :

   ```hiveql
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

8. Dans la barre d’outils, sélectionnez la liste déroulante pour **Envoyer** , afin d’exécuter la tâche. Utilisez l’ **état de la tâche** afin de déterminer si la tâche est terminée.

9. Pour vérifier que le travail a créé une table, utilisez **Explorateur de serveurs** et développez **Azure** > **HDInsight** > votre cluster HDInsight > **Bases de données Hive** > **par défaut**. La table **errorLogs** et la table **log4jLogs** sont répertoriées.

## <a id="nextsteps"></a>Étapes suivantes

Comme vous pouvez le voir, les outils HDInsight pour Visual Studio fournissent un moyen simple de travailler avec des requêtes Hive sur HDInsight.

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les outils de HDInsight pour Visual Studio :

* [Prise en main des outils HDInsight pour Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
