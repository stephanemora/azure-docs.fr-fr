---
title: Utiliser Apache Hive et le Bureau à distance dans HDInsight - Azure
description: Découvrez comment vous connecter à un cluster Hadoop à l'aide du Bureau à distance, et exécuter ensuite des requêtes Hive à l'aide de l'interface de ligne de commande (CLI) Hive.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 60bed4786b61bc96b918511b63ae89daa1cba1c4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217426"
---
# <a name="use-apache-hive-with-apache-hadoop-on-hdinsight-with-remote-desktop"></a>Utilisation d’Apache Hive avec Apache Hadoop sur HDInsight via le Bureau à distance
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous découvrirez comment vous connecter à un cluster HDInsight à l’aide du Bureau à distance, et exécuter ensuite des requêtes Apache Hive à l’aide de l’interface de ligne de commande (CLI) Hive.

> [!IMPORTANT]  
> Le Bureau à distance n’est disponible que sur les clusters HDInsight qui utilisent Windows comme système d’exploitation. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Pour HDInsight 3.4 ou les versions supérieures, consultez l’article [Utilisation d’Apache Hive avec HDInsight et Beeline](apache-hadoop-use-hive-beeline.md) pour plus d’informations sur l’exécution de requêtes Hive directement sur le cluster à partir d’une ligne de commande.

## <a id="prereq"></a>Configuration requise
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* un cluster HDInsight basé sur Windows (Hadoop sur HDInsight)
* Un ordinateur client avec Windows 10, Windows 8 ou Windows 7

## <a id="connect"></a>Connexion avec le Bureau à distance
Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous à lui en suivant les instructions fournies dans [Connexion à des clusters HDInsight avec RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hive"></a>Utilisation de la commande Hive
Une fois connecté au bureau pour le cluster HDInsight, effectuez les étapes suivantes pour utiliser Hive.

1. À partir du bureau HDInsight, démarrez la **ligne de commande Hadoop**.
2. Exécutez la commande suivante pour démarrer l'interface de ligne de commande (CLI) Hive :

        %hive_home%\bin\hive

    Une fois l'interface de ligne de commande lancée, vous verrez apparaître l'invite CLI : `hive>`.
3. En utilisant l'interface de ligne de commande, saisissez les instructions suivantes pour créer une table nommée **log4jLogs** à l'aide d'exemples de données :

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Ces instructions effectuent les opérations suivantes :

   * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
   * **CREATE EXTERNAL TABLE** : crée une nouvelle table externe dans Hive. Les tables externes stockent uniquement la définition de table dans Hive (les données restent à leur emplacement d’origine).

     > [!NOTE]  
     > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe (comme un processus de téléchargement de données automatisé) ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
     >
     > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.
     >
     >
   * **ROW FORMAT** : indique à Hive la façon dont les données sont mises en forme. Dans ce cas, les champs de chaque journal sont séparés par un espace.
   * **STORED AS TEXTFILE LOCATION** : indique à Hive où sont stockées les données (répertoire example/data) et qu’elles sont stockées sous forme de texte.
   * **SELECT** : sélectionne toutes les lignes où la colonne **t4** contient la valeur **[ERROR]**. Cette commande renvoie la valeur **3** , car trois lignes contiennent cette valeur.
   * **INPUT__FILE__NAME LIKE '%.log'** : indique à Hive de retourner uniquement des données provenant de fichiers se terminant par .log. Cela limite la recherche au fichier sample.log qui contient les données et l'empêche de renvoyer des données provenant d'autres fichiers d'exemple qui ne correspondent pas au schéma que nous avons défini.
4. Utilisez les instructions suivantes pour créer une table « interne » nommée **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Ces instructions effectuent les opérations suivantes :

   * **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n’existe pas déjà. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive.

     > [!NOTE]  
     > Contrairement aux tables **EXTERNES** , la suppression d’une table interne entraîne également la suppression des données sous-jacentes.
     >
     >
   * **STORED AS ORC** : stocke les données dans un format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
   * **INSERT OVERWRITE ... SELECT** : sélectionne les lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.

     Pour vérifier que seules les lignes contenant **[ERROR]** dans la colonne t4 ont été stockées dans la table **errorLogs**, utilisez l’instruction suivante afin de renvoyer toutes les lignes à partir de **errorLogs** :

       SELECT * from errorLogs;

     Trois lignes de données doivent normalement être renvoyées. Elles contiennent toutes **[ERROR]** dans la colonne t4.

## <a id="summary"></a>Résumé
Comme vous pouvez le constater, la commande Hive permet d'exécuter facilement et de façon interactive des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

## <a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Si vous utilisez Tez avec Hive, consultez le document suivant pour accéder aux informations de débogage : [Utiliser la vue Tez Apache Ambari sur HDInsight Linux](../hdinsight-debug-ambari-tez-view.md).

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
