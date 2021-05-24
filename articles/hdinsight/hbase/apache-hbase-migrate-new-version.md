---
title: Migrer un cluster HBase vers une nouvelle version - Azure HDInsight
description: Découvrez comment migrer des clusters Apache HBase dans Azure HDInsight vers une version plus récente.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/06/2021
ms.openlocfilehash: 1d6f2d66a00601334a9cab4695b4e78c77a67917
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109654785"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>Effectuer la migration d’un cluster Apache HBase vers une nouvelle version

Cet article explique comment mettre à jour un cluster Apache HBase sur Azure HDInsight vers une version plus récente.

Cet article vaut uniquement si vous utilisez le même compte Stockage Azure pour vos clusters source et de destination. Pour effectuer une mise à niveau avec un compte Stockage nouveau ou différent pour votre cluster de destination, consultez [Migrer Apache HBase vers une nouvelle version avec un nouveau compte Stockage](apache-hbase-migrate-new-version-new-storage-account.md).

En règle générale, le temps d’arrêt dans le cas d’une mise à niveau se limite à quelques minutes. Ce temps d’arrêt est causé par le processus de vidage de toutes les données en mémoire et par le temps nécessaire à la configuration et au redémarrage des services sur le nouveau cluster. Vos résultats peuvent varier en fonction du nombre de nœuds, de la quantité de données et d’autres variables.

## <a name="review-apache-hbase-compatibility"></a>Vérifier la compatibilité d’Apache HBase

Avant de mettre à niveau Apache HBase, vérifiez que la version de HBase présente sur le cluster source est compatible avec celle du cluster de destination. Pour vérifier que votre application est compatible avec la nouvelle version, consultez la matrice de compatibilité et les notes de publication des versions de HBase dans le [Guide de référence HBase](https://hbase.apache.org/book.html#upgrading).

Voici un exemple de matrice de compatibilité des versions. O indique une compatibilité et N une incompatibilité potentielle :

| Type de compatibilité | Version principale| Version secondaire | Correctif |
| --- | --- | --- | --- |
| Compatibilité communication client-serveur | N | Y | O |
| Compatibilité serveur-serveur | N | Y | O |
| Compatibilité format de fichier | N | Y | O |
| Compatibilité API client | N | Y | O |
| Compatibilité fichier binaire client | N | N | O |
| **Compatibilité API limitée côté serveur** |  |  |  |
| Stable | N | Y | O |
| En cours d’évolution | N | N | O |
| Instable | N | N | N |
| Compatibilité dépendance | N | Y | O |
| Compatibilité opérationnelle | N | N | Y |

Pour plus d’informations sur les versions de HDInsight et leur compatibilité, consultez [Versions d’Azure HDInsight](../hdinsight-component-versioning.md).

## <a name="apache-hbase-cluster-migration-overview"></a>Vue d’ensemble de la migration de clusters Apache HBase

Pour mettre à niveau votre cluster Apache HBase sur Azure HDInsight, effectuez les étapes de base suivantes. Pour obtenir des instructions détaillées, reportez-vous aux étapes et commandes détaillées.

Préparez le cluster source :
1. Arrêtez l’ingestion de données.
1. Videz les données des memstores.
1. Arrêtez HBase à partir d’Ambari.
1. Pour les clusters utilisant les écritures accélérées, sauvegardez le répertoire du journal WAL (Write Ahead Log).

Préparez le cluster de destination :
1. Créez le cluster de destination.
1. Arrêtez HBase à partir d’Ambari.
1. Mettez à jour `fs.defaultFS` dans les configurations des services HDFS pour faire référence au conteneur du cluster source d’origine.
1. Pour les clusters utilisant les écritures accélérées, mettez à jour `hbase.rootdir` dans les configurations des services HBase pour faire référence au conteneur du cluster source d’origine.
1. Nettoyez les données Zookeeper.

Effectuer la migration :
1. Nettoyez et migrez le journal WAL.
1. Copiez les applications du conteneur par défaut du cluster de destination vers le conteneur source d’origine.
1. Démarrez tous les services à partir du cluster de destination Ambari.
1. Vérifiez HBase.
1. Supprimez le cluster source.

## <a name="detailed-migration-steps-and-commands"></a>Étapes et commandes de migration détaillées

Aidez-vous de ces étapes et commandes détaillées pour migrer votre cluster Apache HBase.

### <a name="prepare-the-source-cluster"></a>Préparer le cluster source

1. Arrêtez l’ingestion vers le cluster HBase source.
   
1. Videz le cluster HBase source que vous mettez à niveau.
   
   HBase écrit les données entrantes dans un magasin en mémoire appelé *memstore*. Une fois que le memstore atteint une certaine taille, HBase le vide sur le disque pour le stockage à long terme dans le compte de stockage du cluster. La suppression du cluster source après une mise à niveau a également pour effet de supprimer les données contenues dans les memstores. Pour conserver les données, videz manuellement le memstore de chaque table sur le disque avant la mise à niveau.
   
   Vous pouvez vider les données des memstores en exécutant le script [flush_all_tables.sh](https://github.com/Azure/hbase-utils/blob/master/scripts/flush_all_tables.sh) à partir du [dépôt GitHub Azure hbase-utils](https://github.com/Azure/hbase-utils/).
   
   Vous pouvez aussi vider les données des memstores en exécutant la commande shell HBase suivante à partir du cluster HDInsight :
   
   ```bash
   hbase shell
   flush "<table-name>"
   ```
   
1. Connectez-vous à [Apache Ambari](https://ambari.apache.org/) sur le cluster source avec `https://<OLDCLUSTERNAME>.azurehdinsight.net`, puis arrêtez les services HBase.
   
1. À l’invite de confirmation, cochez la case pour activer le mode de maintenance pour HBase.
   
   Pour plus d’informations sur la connexion à Ambari et sur l’utilisation d’Ambari, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](../hdinsight-hadoop-manage-ambari.md).
   
1. Si votre cluster HBase source n’utilise pas la fonctionnalité d’[écritures accélérées](apache-hbase-accelerated-writes.md), passez cette étape. Pour les clusters HBase sources utilisant les écritures accélérées, sauvegardez le répertoire du journal WAL sous HDFS en exécutant les commandes suivantes à partir d’une session SSH sur un nœud Zookeeper ou un nœud Worker du cluster source.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup
   ```
   
### <a name="prepare-the-destination-cluster"></a>Préparer le cluster de destination

1. Sur le portail Azure, [configurez un nouveau cluster HDInsight de destination](../hdinsight-hadoop-provision-linux-clusters.md) en utilisant le même compte de stockage que le cluster source, mais avec un nom de conteneur différent :

   
1. Connectez-vous à [Apache Ambari](https://ambari.apache.org/) sur le nouveau cluster à l’adresse `https://<NEWCLUSTERNAME>.azurehdinsight.net`, puis arrêtez les services HBase.
   
1. Sous **Services** > **HDFS** > **Configs** > **Avanced** > **Advanced core-site**, changez le paramètre HDFS `fs.defaultFS` de façon à le faire pointer vers le nom du conteneur du cluster source d’origine. Par exemple, le paramètre dans la capture d’écran suivante doit être remplacé par `wasbs://hbase-upgrade-old-2021-03-22`.
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png" alt-text="Dans Ambari, sélectionnez Services > HDFS > Configs > Advanced > Advanced core-site et changez le nom du conteneur." border="false":::
   
1. Si votre cluster de destination utilise la fonctionnalité d’écritures accélérées, modifiez le chemin `hbase.rootdir` en le faisant pointer vers le nom du conteneur du cluster source d’origine. Par exemple, le chemin suivant doit être remplacé par `hbase-upgrade-old-2021-03-22`. Si votre cluster n’utilise pas les écritures accélérées, passez cette étape.
   
   :::image type="content" source="./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png" alt-text="Dans Ambari, changez le nom du conteneur pour HBase rootdir." border="true":::
   
1. Nettoyez les données Zookeeper sur le cluster de destination en exécutant les commandes suivantes dans un nœud Zookeeper ou worker :
   
   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit
   ```
   
### <a name="clean-and-migrate-wal"></a>Nettoyer et migrer le journal WAL

Exécutez les commandes suivantes en fonction de votre version de HDI source et selon que les clusters source et de destination utilisent ou non les écritures accélérées.

- Le cluster de destination utilise toujours HDI version 4.0, car HDI 3.6 est en Support De base et n’est pas recommandé pour les nouveaux clusters.
- La commande de copie HDFS est `hdfs dfs <copy properties starting with -D> -cp <source> <destination> # Serial execution`.

> [!NOTE]  
> - Le `<source-container-fullpath>` pour le stockage de type WASB est `wasbs://<source-container-name>@<storageaccountname>.blob.core.windows.net`.
> - Le `<source-container-fullpath>` pour le stockage de type Azure Data Lake Storage Gen2 est `abfs://<source-container-name>@<storageaccountname>.dfs.core.windows.net`.

- [Le cluster source utilise HDI 3.6 avec les écritures accélérées et le cluster de destination utilise les écritures accélérées](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination utilise des écritures accélérées](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).
- [Le cluster source utilise HDI 4.0 avec les écritures accélérées et le cluster de destination utilise les écritures accélérées](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination utilise les écritures accélérées](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).

#### <a name="the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>Le cluster source utilise HDI 3.6 ou HDI 4.0 avec les écritures accélérées et le cluster de destination utilise les écritures accélérées

Nettoyez les données FS du journal WAL pour le cluster de destination et copiez le répertoire du journal WAL du cluster source vers le HDFS du cluster de destination. Copiez le répertoire en exécutant les commandes suivantes dans un nœud Zookeeper ou worker sur le cluster de destination :

```bash   
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wal-backup/hbasewal hdfs://mycluster/
```
#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination utilise les écritures accélérées

Nettoyez les données FS du journal WAL pour le cluster de destination et copiez le répertoire du journal WAL du cluster source vers le HDFS du cluster de destination. Copiez le répertoire en exécutant les commandes suivantes dans un nœud Zookeeper ou worker sur le cluster de destination :

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container>/hbase/*WALs hdfs://mycluster/hbasewal
```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées

Nettoyez les données FS du journal WAL pour le cluster de destination et copiez le répertoire du journal WAL du cluster source vers le HDFS du cluster de destination. Pour copier le répertoire, exécutez les commandes suivantes dans un nœud Zookeeper ou worker sur le cluster de destination :

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs" -cp <source-container-fullpath>/hbase/*WALs /hbase-wals
```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination utilise les écritures accélérées

Nettoyez les données FS du journal WAL pour le cluster de destination et copiez le répertoire du journal WAL du cluster source vers le HDFS du cluster de destination. Copiez le répertoire en exécutant les commandes suivantes dans un nœud Zookeeper ou worker sur le cluster de destination :

```bash
sudo -u hbase hdfs dfs -rm -r hdfs://mycluster/hbasewal
sudo -u hbase hdfs dfs -cp <source-container-fullpath>/hbase-wals/* hdfs://mycluster/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées

Nettoyez les données FS du journal WAL pour le cluster de destination et copiez le répertoire du journal WAL du cluster source vers le HDFS du cluster de destination. Pour copier le répertoire, exécutez les commandes suivantes dans un nœud Zookeeper ou worker sur le cluster de destination :

```bash
sudo -u hbase hdfs dfs -rm -r /hbase-wals/*
sudo -u hbase hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals /
```

### <a name="complete-the-migration"></a>Effectuer la migration

1. En utilisant le contexte utilisateur `sudo -u hdfs`, copiez le dossier `/hdp/apps/<new-version-name>` et son contenu de `<destination-container-fullpath>` vers le dossier `/hdp/apps` sous `<source-container-fullpath>`. Vous pouvez copier le dossier en exécutant les commandes suivantes sur le cluster de destination :
   
   ```bash   
   sudo -u hdfs hdfs dfs -cp /hdp/apps/<hdi-version> <source-container-fullpath>/hdp/apps
   ```
   
   Par exemple :
   ```bash
   sudo -u hdfs hdfs dfs -cp /hdp/apps/4.1.3.6 wasbs://hbase-upgrade-old-2021-03-22@hbaseupgrade.blob.core.windows.net/hdp/apps
   ```
   
1. Sur le cluster de destination, enregistrez vos modifications et redémarrez tous les services nécessaires indiqués par Ambari.
   
1. Faites pointer votre application vers le cluster de destination.
   
   > [!NOTE]  
   > Le nom DNS statique de votre application change pendant la mise à niveau. Au lieu de coder en dur ce nom DNS, vous pouvez configurer un CNAME dans les paramètres DNS de votre nom de domaine qui pointe vers le nom du cluster. Une autre option consiste à utiliser un fichier config pour votre application, que vous pouvez mettre à jour sans nouveau déploiement.
   
1. Lancez l’ingestion.
   
1. Vérifiez la cohérence HBase et les opérations simples de langage de définition de données (DDL) et de langage de manipulation de données (DDL).
   
1. Si le cluster de destination est satisfaisant, supprimez le cluster source.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur [Apache HBase](https://hbase.apache.org/) et la mise à niveau de clusters HDInsight, consultez les articles suivants :

- [Mettre à niveau le cluster HDInsight](../hdinsight-upgrade-cluster.md)
- [Superviser et gérer Azure HDInsight à l’aide de l’interface utilisateur web d’Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
- [Versions d’Azure HDInsight](../hdinsight-component-versioning.md)
- [Optimiser Apache HBase](../optimize-hbase-ambari.md)
