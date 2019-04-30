---
title: Utiliser les vues Apache Ambari pour travailler avec Hive sur HDInsight (Apache Hadoop) - Azure
description: Découvrez comment utiliser la vue Hive à partir de votre navigateur web pour envoyer des requêtes Hive. La vue Hive fait partie de l’interface utilisateur web Ambari fournie avec votre cluster HDInsight sous Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 55f8f453faf35d52c5c292e6b309194443980466
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095317"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Utiliser la vue Apache Ambari Hive avec Apache Hadoop dans HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Découvrez comment exécuter des requêtes Hive avec la vue Apache Ambari Hive. L’affichage Hive vous permet de créer, d’optimiser et d’exécuter des requêtes Hive à partir du navigateur web.

## <a name="prerequisites"></a>Conditions préalables

* Un cluster Hadoop sur HDInsight. Consultez [prise en main HDInsight sous Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Un navigateur web

## <a name="run-a-hive-query"></a>Exécution d'une tâche Hive

1. À partir de la [Azure portal](https://portal.azure.com/), sélectionnez votre cluster.  Consultez [liste et affichage des clusters](../hdinsight-administer-use-portal-linux.md#showClusters) pour obtenir des instructions. Le cluster est ouvert dans un nouveau panneau du portail.

2. À partir de **des tableaux de bord de Cluster**, sélectionnez **Ambari views**. Lorsque vous êtes invité à vous authentifier, utilisez le nom de compte et le mot de passe de connexion de cluster (`admin` par défaut) que vous avez fournis lors de la création du cluster.

3. Dans la liste des vues, sélectionnez __Affichage Hive__.

    ![L’affichage Hive sélectionné](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    L’affiche Hive est similaire à ceci :

    ![Image de la feuille de calcul de requête pour l’affichage Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Dans l’onglet __Requête__ , collez les instructions HiveQL suivantes dans la feuille de calcul :

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Ces instructions effectuent les opérations suivantes :

   * `DROP TABLE`: Supprime la table et le fichier de données, si la table existe déjà.

   * `CREATE EXTERNAL TABLE`: Crée une nouvelle table « externe » dans Hive.
     Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.

   * `ROW FORMAT`: Montre la mise en forme des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.

   * `STORED AS TEXTFILE LOCATION`: Montre où sont stockées les données, et qu’elles sont stockées sous forme de texte.

   * `SELECT`: Sélectionne toutes les lignes où la colonne t4 contient la valeur [ERROR].

   > [!IMPORTANT]  
   > Conservez la sélection __Base de données__ par __défaut__. Les exemples de ce document utilisent la base de données par défaut incluse avec HDInsight.

5. Pour démarrer la requête, sélectionnez **Execute** ci-dessous la feuille de calcul. Ce bouton devient orange et le texte affiche **Stop** (Arrêter).

6. Lorsque la requête est terminée, l’onglet **Results** (Résultats) affiche les résultats de l’opération. Le texte suivant est le résultat de la requête :

        loglevel       count
        [ERROR]        3

    Vous pouvez utiliser la **journal** onglet pour afficher les informations de journalisation que la tâche créée.

   > [!TIP]  
   > Télécharger ou enregistrer les résultats à partir de la **Actions** boîte de dialogue de la liste déroulante sous la **résultats** onglet.

### <a name="visual-explain"></a>Visual Explain

Pour afficher une visualisation du plan de requête, sélectionnez l’onglet **Visual Explain** au-dessous de la feuille de calcul.

La vue **Visual Explain** de la requête peut être utile pour comprendre le déroulement de requêtes complexes.

### <a name="tez-ui"></a>Interface utilisateur Tez

Pour afficher l’UI Tez pour la requête, sélectionnez le **Tez UI** onglet situé sous la feuille de calcul.

> [!IMPORTANT]  
> Tez n’est pas utilisé pour résoudre toutes les requêtes. Vous pouvez résoudre de nombreuses requêtes sans utiliser Tez. 

## <a name="view-job-history"></a>Afficher l’historique des tâches

L’onglet __Travaux__ affiche un historique des requêtes Hive.

![Image de l’historique des travaux](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tables de base de données

Vous pouvez utiliser l’onglet __Tables__ onglet pour travailler avec des tables dans une base de données Hive.

![Image de l’onglet tables](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Requêtes enregistrées

À partir de l’onglet **Query** (Requête), vous pouvez éventuellement enregistrer des requêtes. Après avoir enregistré une requête, vous pouvez la réutiliser à partir de l’onglet __Saved Queries__ (Requêtes enregistrées).

![Image de l’onglet de requêtes enregistrées](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Les requêtes enregistrées sont stockées dans le stockage en cluster par défaut. Vous pouvez rechercher les requêtes enregistrées sous le chemin `/user/<username>/hive/scripts`. Ces fichiers sont stockés en tant que fichiers `.hql` en texte brut.
>
> Si vous supprimez le cluster, tout en conservant le stockage, vous pouvez employer un utilitaire tel que [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) ou l’Explorateur de stockage Data Lake (à partir du [portail Azure](https://portal.azure.com)) pour récupérer les requêtes.

## <a name="user-defined-functions"></a>Fonctions définies par l’utilisateur

Vous pouvez étendre Hive par l’intermédiaire de fonctions définies par l’utilisateur. Utilisez une fonction définie par l’utilisateur pour implémenter une fonctionnalité ou une logique qui n’est pas facilement modélisée en HiveQL.

Déclarez et enregistrez un ensemble de fonctions définies par l’utilisateur en utilisant l’onglet **UDF** en haut de l’affichage Hive. Ces UDF peuvent être utilisés avec les **l’éditeur de requête**.

![Image de l’onglet UDF](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Lorsque vous avez ajouté une fonction définie par l’utilisateur à l’affichage Hive, un bouton **Insert udfs** (Insérer des fonctions définies par l’utilisateur) s’affiche au bas de **Query Editor** (l’éditeur de requête). La sélection de cette entrée affiche une liste déroulante des UDF définis dans l’affichage Hive. La sélection d’une fonction UDF ajoute des instructions HiveQL à votre requête pour activer l’UDF.

Par exemple, si vous avez défini une fonction UDF avec les propriétés suivantes :

* Nom de ressource : myudfs

* Chemin d’accès à la ressource : /myudfs.jar

* Nom de la fonction UDF : myawesomeudf

* Nom de la classe UDF : com.myudfs.Awesome

L’utilisation du bouton **Insert udfs** (Insérer des fonctions définies par l’utilisateur) affiche une entrée nommée **myudfs** avec une autre liste déroulante pour chaque fonction définie par l’utilisateur qui est paramétrée pour cette ressource. Dans le cas présent, il s’agit de **myawesomeudf**. La sélection de cette entrée ajoute le code suivant au début de la requête :

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Vous pouvez ensuite utiliser la fonction UDF dans votre requête. Par exemple : `SELECT myawesomeudf(name) FROM people;`.

Pour plus d’informations sur l’utilisation des fonctions définies par l’utilisateur avec Hive sur HDInsight, consultez les articles suivants :

* [Utilisation de Python UDF avec Apache Hive et Apache Pig dans HDInsight](python-udf-hdinsight.md)
* [Comment ajouter une UDF personnalisée Apache Hive à HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Paramètres Hive

Vous pouvez modifier différents paramètres Hive, par exemple pour passer du moteur d’exécution Tez (par défaut) à MapReduce pour Hive.

## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utiliser Apache Hive avec Apache Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utiliser Apache Pig avec Apache Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utiliser MapReduce avec Apache Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
