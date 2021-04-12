---
title: Migrer le metastore Hive par défaut vers le metastore externe sur Azure HDInsight
description: Migrer le metastore Hive par défaut vers le metastore externe sur Azure HDInsight
author: kevxmsft
ms.author: kevx
ms.reviewer: ''
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 4a0258d5e448c59baa1cd63e98058fe7116a8485
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566113"
---
# <a name="migrate-default-hive-metastore-db-to-external-metastore-db"></a>Migration de la base de données metastore Hive DB par défaut vers une base de données metastore externe

Cet article explique comment migrer les métadonnées d’une [base de données metastore par défaut](../hdinsight-use-external-metadata-stores.md#default-metastore) pour Hive vers une base de données SQL Database externe sur HDInsight. 

## <a name="why-migrate-to-external-metastore-db"></a>Pourquoi migrer vers une base de base de données metastore externe

* La base de données metastore par défaut est limitée à la référence SKU de base. Elle ne peut pas gérer les charges de travail de production.

* La base de données metastore externe permet au client d’effectuer un scale-out des ressources de calcul Hive en ajoutant de nouveaux clusters HDInsight qui partagent la même base de données metastore.

* Pour la migration de HDInsight 3.6 à HDInsight 4.0, il est obligatoire de migrer les métadonnées vers la base de données metastore externe avant de mettre à niveau la version du schéma Hive. Consultez [Migration de charges de travail de HDInsight 3.6 à HDInsight 4.0](./apache-hive-migrate-workloads.md).

La base de données metastore par défaut dispose d’une capacité de calcul limitée. Nous vous recommandons donc une faible utilisation par les autres travaux sur le cluster lors de la migration des métadonnées.

Les bases de données source et cible doivent utiliser la même version de HDInsight et les mêmes comptes de stockage. Si vous mettez à niveau HDInsight de la version 3.6 à la version 4.0, effectuez d’abord la procédure indiquée dans cet article. Ensuite, suivez les [étapes officielles de mise à niveau](./apache-hive-migrate-workloads.md).

## <a name="prerequisites"></a>Prérequis

Si vous utilisez [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md), il est probable que les emplacements des tables Hive dépendent des configurations HDFS du cluster. Exécutez l’action de script suivante pour rendre ces emplacements portables vers d’autres clusters. Consultez [Action de script sur un cluster en cours d’exécution](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

L’action est semblable au remplacement de liens symboliques par leur chemin complet.

|Propriété | Valeur |
|---|---|
|URI de script bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
|Type(s) de nœud|Head|
|Paramètres|""|

## <a name="migrate-with-exportimport-using-sqlpackage"></a>Migration avec Import/Export à l’aide de sqlpackage

Un cluster HDInsight créé après le 15/10/2020 ne prend en charge la fonctionnalité SQL Export/Import pour la base de données metastore Hive par défaut qu’en utilisant `sqlpackage`.

1. Installez [sqlpackage](/sql/tools/sqlpackage-download#get-sqlpackage-net-core-for-linux) sur le cluster.

2. Exportez la base de données metastore par défaut dans le fichier BACPAC en exécutant la commande suivante.

    ```bash
    wget "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive_metastore_tool.py"
    SQLPACKAGE_FILE='/home/sshuser/sqlpackage/sqlpackage'  # replace with sqlpackage location
    TARGET_FILE='hive.bacpac'
    sudo python hive_metastore_tool.py --sqlpackagefile $SQLPACKAGE_FILE --targetfile $TARGET_FILE
    ```

3. Enregistrez le fichier BACPAC, par exemple avec les commandes suivantes.

    ```bash
    hdfs dfs -mkdir -p /bacpacs
    hdfs dfs -put $TARGET_FILE /bacpacs/
    ```

4. Importez le fichier BACPAC dans une nouvelle base de données en suivant [cette procédure](../../azure-sql/database/database-import.md).

5. La nouvelle base de données est prête à être [configurée en tant que base de données metastore externe sur un nouveau cluster HDInsight](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation).

## <a name="migrate-using-hive-script"></a>Migration à l’aide d’un script Hive

Les clusters créés avant le 15/10/2020 ne prennent pas en charge l’exportation ni l’importation de la base de données metastore par défaut.

Pour ces clusters, suivez le guide [Copie de tables Hive entre différents comptes de stockage](./hive-migration-across-storage-accounts.md), en utilisant un second cluster avec une [base de données metastore Hive externe](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation). Le second cluster peut être associé au même compte de stockage, mais doit utiliser un nouveau système de fichiers par défaut.

### <a name="option-to-shallow-copy"></a>Option de copie « superficielle »
La consommation de stockage doublerait en cas de copie complète des tables suivant le guide ci-dessus. Vous auriez alors à nettoyer manuellement les données dans le conteneur de stockage source.
Il est possible d’effectuer plutôt une copie « superficielle » des tables si elles ne sont pas transactionnelles. Dans HDInsight 3.6, toutes les tables Hive sont non transactionnelles par défaut. En revanche, seules les tables externes sont non transactionnelles dans HDInsight 4.0. Les tables transactionnelles doivent subir une copie complète. Procédez comme suit pour effectuer une copie superficielle de tables non transactionnelles :

1. Exécutez le script [hive-ddls.sh](https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-ddls.sh) sur le nœud principal primaire du cluster source pour générer le DDL de chaque table Hive.
2. Le DDL est écrit dans un script Hive local nommé `/tmp/hdi_hive_ddls.hql`. Exécutez-le sur le cluster cible qui utilise une base de données metastore Hive externe.

## <a name="verify-that-all-hive-tables-are-imported"></a>Vérification de l’importation de toutes les tables Hive

La commande suivante effectue une requête SQL sur la base de données metastore pour imprimer toutes les tables Hive et leurs emplacements de données. Comparez les sorties entre les nouveaux et les anciens clusters pour vérifier qu’il ne manque aucune table dans la nouvelle base de données metastore.

```bash
SCRIPT_FNAME='hive_metastore_tool.py'
SCRIPT="/tmp/$SCRIPT_FNAME"
wget -O "$SCRIPT" "https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/$SCRIPT_FNAME"
OUTPUT_FILE='/tmp/hivetables.csv'
QUERY="SELECT DBS.NAME, TBLS.TBL_NAME, SDS.LOCATION FROM SDS, TBLS, DBS WHERE TBLS.SD_ID = SDS.SD_ID AND TBLS.DB_ID = DBS.DB_ID ORDER BY DBS.NAME, TBLS.TBL_NAME ASC;"
sudo python "$SCRIPT" --query "$QUERY" > $OUTPUT_FILE
```

## <a name="further-reading"></a>Pour aller plus loin

* [Migration de charges de travail de HDInsight 3.6 à HDInsight 4.0](./apache-hive-migrate-workloads.md)
* [Migration de charges de travail Hive entre différents comptes de stockage](./hive-migration-across-storage-accounts.md)
* [Connexion à Beeline sur HDInsight](../hadoop/connect-install-beeline.md)
* [Résolution de l’erreur d’autorisation lors de la création d’une table](./interactive-query-troubleshoot-permission-error-create-table.md)