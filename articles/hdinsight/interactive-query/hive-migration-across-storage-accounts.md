---
title: Migration de la charge de travail Hive vers un nouveau compte dans Stockage Azure
description: Migration de la charge de travail Hive vers un nouveau compte dans Stockage Azure
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/11/2020
ms.openlocfilehash: 0d62bebddb7751c168ba2e487b2391a40bbc6e67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743538"
---
# <a name="hive-workload-migration-to-new-account-in-azure-storage"></a>Migration de la charge de travail Hive vers un nouveau compte dans Stockage Azure

Découvrez comment utiliser des actions de script pour copier des tables Hive entre des comptes de stockage dans HDInsight. Cela peut être utile lors de la migration vers [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

Pour copier manuellement une table Hive individuelle sur HDInsight 4.0, consultez [Exportation/importation Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport).

## <a name="prerequisites"></a>Prérequis

* Un nouveau cluster HDInsight avec les configurations suivantes :
  * Son système de fichiers par défaut se trouve dans le compte de stockage cible. Consultez [Utiliser Stockage Azure avec des clusters Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md).
  * Sa version de cluster doit correspondre à celle du cluster source.
  * Il utilise une nouvelle base de données metastore Hive externe. Consultez [Utiliser des magasins de métadonnées externes](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).
* Un compte de stockage accessible aux clusters d’origine et aux nouveaux. Consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](../hdinsight-hadoop-add-storage.md) et [Types de stockage et fonctionnalités](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features) pour connaître les types de stockage secondaires autorisés.

    Voici quelques options possibles :
  * Ajoutez le compte de stockage cible au cluster d’origine.
  * Ajoutez le compte de stockage d’origine au nouveau cluster.
  * Ajoutez un compte de stockage intermédiaire aux clusters d’origine et aux nouveaux.

## <a name="how-it-works"></a>Fonctionnement

Nous allons exécuter une action de script pour exporter les tables Hive du cluster d’origine vers un répertoire HDFS spécifié. Consultez [Action de script sur un cluster en cours d’exécution](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

Nous exécuterons ensuite une autre action de script sur le nouveau cluster pour importer les tables Hive du répertoire HDFS.

Le script recrée les tables dans le système de fichiers par défaut du nouveau cluster. Les données des tables natives sont également copiées dans le stockage. Les tables non natives sont copiées uniquement par définition. Consultez [Gestionnaires de stockage Hive](https://cwiki.apache.org/confluence/display/Hive/StorageHandlers) pour plus d’informations sur les tables non natives.

Le chemin d’accès des tables externes qui ne se trouvent pas dans le répertoire d’entrepôt Hive est conservé. Les autres tables sont copiées dans le chemin d’accès Hive par défaut du cluster cible. Consultez les propriétés Hive `hive.metastore.warehouse.external.dir` et `hive.metastore.warehouse.dir`.

Les scripts ne conservent pas les autorisations de fichier personnalisées dans le cluster cible.

> [!NOTE]
>
> Ce guide prend en charge la copie des objets de métadonnées liés aux bases de données, tables et partitions Hive. Les autres objets de métadonnées doivent être recréés manuellement.
>
> * Pour `Views`, Hive prend en charge la commande `SHOW VIEWS` à partir de Hive 2.2.0 sur HDInsight 4.0. Utilisez `SHOW CREATE TABLE` pour la définition de vue. Pour les versions antérieures de Hive, interrogez la base de données SQL du metastore pour afficher les vues.
> * Pour `Materialized Views`, utilisez les commandes `SHOW MATERIALIZED VIEWS`, `DESCRIBE FORMATTED` et `CREATE MATERIALIZED VIEW`. Consultez [Vues matérialisées](https://cwiki.apache.org/confluence/display/Hive/Materialized+views) pour plus d’informations.
> * Pour `Constraints`, pris en charge à partir de Hive 2.1.0 sur HDInsight 4.0, utilisez `DESCRIBE EXTENDED` pour répertorier les contraintes d’une table, et utilisez `ALTER TABLE` pour ajouter des contraintes. Consultez [Modifier les contraintes de table](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTableConstraints) pour plus d’informations.

## <a name="copy-hive-tables"></a>Copier des tables Hive

1. Appliquez l’action de script « export » sur le cluster d’origine avec les champs suivants.

    Cela génère et exécute des scripts Hive intermédiaires. Ils seront enregistrés dans le `<hdfs-export-path>` spécifié.

    Si vous le souhaitez, vous pouvez utiliser `--run-script=false` pour les personnaliser avant l’exécution manuelle.

    |Propriété | Valeur |
    |---|---|
    |URI de script bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/export-hive-data-v01.sh`|
    |Type(s) de nœud|Head|
    |Paramètres|`<hdfs-export-path>` `--run-script`|

    ```sh
    usage: generate Hive export and import scripts and export Hive data to specified HDFS path
           [--run-script={true,false}]
           hdfs-export-path

    positional arguments:

        hdfs-export-path      remote HDFS directory to write export data to

    optional arguments:
        --run-script={true,false}
                            whether to execute the generated Hive export script
                            (default: true)
    ```

2. Une fois l’exportation terminée, appliquez l’action de script « import » sur le nouveau cluster avec les champs suivants.

    |Propriété | Valeur |
    |---|---|
    |URI de script bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/import-hive-data-v01.sh`|
    |Type(s) de nœud|Head|
    |Paramètres|`<hdfs-export-path>`|

    ```sh
    usage: download Hive import script from specified HDFS path and execute it
           hdfs-export-path

    positional arguments:

      hdfs-export-path      remote HDFS directory to download Hive import script from

    ```

## <a name="verification"></a>Vérification

Téléchargez et exécutez le script en tant qu’utilisateur racine [`hive_contents.sh`](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_contents.sh) sur le nœud principal de chaque cluster, puis comparez le contenu du fichier de sortie `/tmp/hive_contents.out`. Consultez [Connect to HDInsight (Apache Hadoop) using SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) (Se connecter à HDInsight (Apache Hadoop) avec SSH).

## <a name="cleanup-additional-storage-usage"></a>Nettoyer l’utilisation du stockage supplémentaire

Une fois la migration du stockage terminée et vérifiée, vous pouvez supprimer les données dans le chemin d’exportation HDFS spécifié.

L’option consiste à utiliser la commande HDFS `hdfs dfs -rm -R`.

## <a name="option-reduce-additional-storage-usage"></a>Option : réduire l’utilisation du stockage supplémentaire

L’action de script d’exportation est susceptible de doubler l’utilisation du stockage en raison de Hive. Il est toutefois possible de limiter l’utilisation du stockage supplémentaire en migrant manuellement une base de données ou une table à la fois.

1. Spécifiez `--run-script=false` pour ignorer l’exécution du script Hive généré. Les scripts d’exportation et d’importation Hive seront toujours enregistrés dans le chemin d’accès d’exportation.

2. Exécutez des extraits des scripts d’exportation et d’importation Hive base de données par base de données ou table par table, en nettoyant manuellement le chemin d’accès d’exportation après chaque base de données ou table migrée.

## <a name="next-steps"></a>Étapes suivantes

* [Azure Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Utiliser des magasins de métadonnées externes](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)
* [Types de stockage et fonctionnalités](../hdinsight-hadoop-compare-storage-options.md#storage-types-and-features)
* [Action de script sur un cluster en cours d’exécution](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
