---
title: Migrer des charges de travail Azure HDInsight 3.6 Hive vers HDInsight 4.0
description: Découvrez comment migrer des charges de travail Apache Hive de HDInsight 3.6 vers HDInsight 4.0.
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: 43d616bc82c608918f5e7ee51481a393dd55a284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566068"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrer des charges de travail Azure HDInsight 3.6 Hive vers HDInsight 4.0

HDInsight 4.0 a plusieurs avantages par rapport à HDInsight 3.6. Voici une [vue d’ensemble des nouveautés dans HDInsight 4.0](../hdinsight-version-release.md).

Cet article décrit les étapes à suivre pour migrer des charges de travail Hive de HDInsight 3.6 vers 4.0, notamment

* Mettre à niveau le schéma et copier le metastore Hive
* Migration sécurisée pour la compatibilité ACID
* Préservation des stratégies de sécurité Hive

Les nouveaux et anciens clusters HDInsight doivent avoir accès aux mêmes comptes de stockage.

La migration des tables Hive vers un nouveau compte de stockage doit être effectuée dans le cadre d’une étape distincte. Consultez [Migration Hive entre des comptes de stockage](./hive-migration-across-storage-accounts.md).

## <a name="steps-to-upgrade"></a>Procédure de mise à niveau

### <a name="1-prepare-the-data"></a>1. Préparer les données

* Par défaut, HDInsight 3.6 ne prend pas en charge les tables ACID. Toutefois, si des tables ACID sont présentes, exécutez la compression « MAJOR » dessus. Pour plus d’informations sur le compactage, consultez le [Manuel d’utilisation du langage Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact).

* Si vous utilisez [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md), les emplacements des tables Hive dépendent probablement des configurations HDFS du cluster. Exécutez l’action de script suivante pour rendre ces emplacements portables vers d’autres clusters. Consultez [Action de script sur un cluster en cours d’exécution](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster).

    |Propriété | Valeur |
    |---|---|
    |URI de script bash|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |Type(s) de nœud|Head|
    |Paramètres||

### <a name="2-copy-the-sql-database"></a>2. Copier la base de données SQL

* Si le cluster utilise un metastore Hive par défaut, suivez ce [guide](./hive-default-metastore-export-import.md) pour exporter des métadonnées vers un metastore externe. Ensuite, créez une copie du metastore externe pour la mise à niveau.

* Si le cluster utilise un metastore Hive externe, créez-en une copie. Les options incluent l’[exportation/importation](../../azure-sql/database/database-export.md) et la [limite de restauration dans le temps](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore).

### <a name="3-upgrade-the-metastore-schema"></a>3. Mettre à niveau le schéma du metastore

Cette étape utilise [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) partir de HDInsight 4.0 pour mettre à niveau le schéma du metastore.

> [!Warning]
> Cette étape est irréversible. Exécutez-la uniquement sur une copie du metastore.

1. Créez un cluster HDInsight 4.0 temporaire pour accéder à Hive 4.0 `schematool`. Vous pouvez utiliser le [metastore Hive par défaut](../hdinsight-use-external-metadata-stores.md#default-metastore) pour cette étape.

1. À partir du cluster HDInsight 4.0, exécutez `schematool` pour mettre à niveau le metastore HDInsight 3.6 cible :

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > Cet utilitaire utilise le client `beeline` pour exécuter des scripts SQL dans `/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql`.
    >
    > La syntaxe SQL dans ces scripts n’est pas nécessairement compatible avec d’autres outils clients. Par exemple, [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) et l’[éditeur de requêtes sur le portail Azure](../../azure-sql/database/connect-query-portal.md) requièrent le mot clé `GO` après chaque commande.
    >
    > Si un script échoue en raison de la capacité des ressources ou des délais d’expiration des transactions, effectuez un scale-up de SQL Database.

1. Vérifiez la version finale avec la requête `select schema_version from dbo.version`.

    La sortie doit correspondre à celle de la commande bash suivante à partir du cluster HDInsight 4.0.

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. Supprimez le cluster HDInsight 4.0 temporaire.

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. Déployer un nouveau cluster HDInsight 4.0

Créez un nouveau cluster HDInsight 4.0, en [sélectionnant le metastore Hive mis à niveau](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation) et les mêmes comptes de stockage.

* Le nouveau cluster ne doit pas nécessairement avoir le même système de fichiers par défaut.

* Si le metastore contient des tables résidant dans plusieurs comptes de stockage, vous devez ajouter ces comptes de stockage au nouveau cluster pour accéder à ces tables. Consultez [Ajouter des comptes de stockage supplémentaires à HDInsight](../hdinsight-hadoop-add-storage.md).

* Si les travaux Hive échouent en raison d’une inaccessibilité du stockage, vérifiez que l’emplacement de la table se trouve dans un compte de stockage ajouté au cluster.

    Utilisez la commande Hive suivante pour identifier l’emplacement de la table :

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5. Convertir les tables pour la conformité ACID

Les tables managées doivent être compatibles ACID sur HDInsight 4.0. Exécutez `strictmanagedmigration` sur HDInsight 4.0 pour convertir toutes les tables managées non ACID en tables externes avec la propriété `'external.table.purge'='true'`. Exécutez depuis le nœud principal :

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>Sécuriser Hive sur les versions HDInsight

HDInsight s’intègre de manière facultative avec Azure Active Directory à l’aide du Pack Sécurité Entreprise (ESP) HDInsight. ESP utilise Kerberos et Apache Ranger à gérer les autorisations de ressources spécifiques au sein du cluster. Les stratégies Ranger déployées sur Hive dans HDInsight 3.6 peuvent être migrées vers HDInsight 4.0 en procédant comme suit :

1. Accédez au panneau Ranger Service Manager dans votre cluster HDInsight 3.6.
2. Accédez à la stratégie nommée **HIVE** et exportez la stratégie vers un fichier json.
3. Assurez-vous que tous les utilisateurs référencés dans le fichier json de stratégie exporté existent dans le nouveau cluster. Si un utilisateur est référencé dans le fichier json de stratégie, mais n’existe pas dans le nouveau cluster, ajoutez l’utilisateur dans le nouveau cluster ou supprimez la référence de la stratégie.
4. Accédez au panneau **Ranger Service Manager** de votre cluster HDInsight 4.0.
5. Accédez à la stratégie nommée **HIVE** et importez le fichier json de stratégie Ranger de l’étape 2.

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>Modifications Hive dans HDInsight 4.0 qui peuvent nécessiter des modifications d’application

* Consultez [Configuration supplémentaire à l’aide de Hive Warehouse Connector](./apache-hive-warehouse-connector.md) pour le partage du metastore entre Spark et Hive pour les tables ACID.

* HDInsight 4.0 utilise l’[autorisation basée sur le stockage](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server). Si vous modifiez les autorisations de fichier ou créez des dossiers en tant qu’utilisateur autre que Hive, vous risquez d’obtenir des erreurs Hive basées sur les autorisations de stockage. Pour résoudre ce problème, accordez l’accès `rw-` à l’utilisateur. Consultez [Guide des autorisations HDFS](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

* `HiveCLI` est remplacé par `Beeline`.

Consultez [Annonce HDInsight 4.0](../hdinsight-version-release.md) pour connaître les autres modifications.

## <a name="further-reading"></a>Pour aller plus loin

* [Annonce d’HDInsight 4.0](../hdinsight-version-release.md)
* [Présentation approfondie d’HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tables ACID Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)