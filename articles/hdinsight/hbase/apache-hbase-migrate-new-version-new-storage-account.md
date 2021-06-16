---
title: Migrer un cluster HBase vers une nouvelle version et un compte Stockage – Azure HDInsight
description: Découvrez comment migrer un cluster Apache HBase dans Azure HDInsight vers une version plus récente avec un autre compte Stockage Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 05/06/2021
ms.openlocfilehash: 4ed4de8c134575bba1b961f918216eb3cca9b6b1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111963996"
---
# <a name="migrate-apache-hbase-to-a-new-version-and-storage-account"></a>Migrer Apache HBase vers une nouvelle version et un compte de stockage

Cet article explique comment mettre à jour votre cluster Apache HBase sur Azure HDInsight vers une version plus récente avec un autre compte Stockage Azure.

Cet article vaut uniquement si vous avez besoin d’utiliser des comptes Stockage différents pour vos clusters source et de destination. Pour mettre à niveau des versions avec un même compte Stockage pour vos clusters source et de destination, consultez [Migrer Apache HBase vers une nouvelle version](apache-hbase-migrate-new-version.md).

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

Les notes de publication des versions de HBase doivent décrire les incompatibilités cassantes. Testez votre application dans un cluster exécutant la version cible de HDInsight et HBase.

Pour plus d’informations sur les versions de HDInsight et leur compatibilité, consultez [Versions d’Azure HDInsight](../hdinsight-component-versioning.md).

## <a name="apache-hbase-cluster-migration-overview"></a>Vue d’ensemble de la migration de clusters Apache HBase

Pour mettre à niveau et migrer votre cluster Apache HBase sur Azure HDInsight vers un nouveau compte de stockage, effectuez les étapes de base suivantes. Pour obtenir des instructions détaillées, reportez-vous aux étapes et commandes détaillées.

Préparez le cluster source :
1. Arrêtez l’ingestion de données.
1. Videz les données de memstore.
1. Arrêtez HBase à partir d’Ambari.
1. Pour les clusters utilisant les écritures accélérées, sauvegardez le répertoire du journal WAL (Write Ahead Log).

Préparez le cluster de destination :
1. Créez le cluster de destination.
1. Arrêtez HBase à partir d’Ambari.
1. Nettoyez les données Zookeeper.
1. Faites basculer l’utilisateur vers HBase.

Effectuez la migration :
1. Nettoyez le système de fichiers de destination, migrez les données, puis supprimez `/hbase/hbase.id`.
1. Nettoyez et migrez le journal WAL.
1. Démarrez tous les services à partir du cluster de destination Ambari.
1. Vérifiez HBase.
1. Supprimez le cluster source.

## <a name="detailed-migration-steps-and-commands"></a>Étapes et commandes de migration détaillées

Aidez-vous de ces étapes et commandes détaillées pour migrer votre cluster Apache HBase avec un nouveau compte de stockage.

### <a name="prepare-the-source-cluster"></a>Préparer le cluster source

1. Arrêtez l’ingestion vers le cluster HBase source.
   
1. Videz le cluster HBase source que vous mettez à niveau.
   
   HBase écrit les données entrantes dans un magasin en mémoire appelé *memstore*. Une fois que le memstore atteint une certaine taille, HBase le vide sur le disque pour le stockage à long terme dans le compte de stockage du cluster. La suppression du cluster source après une mise à niveau a également pour effet de supprimer les données contenues dans les memstores. Pour conserver les données, videz manuellement le memstore de chaque table sur le disque avant la mise à niveau.
   
   Vous pouvez vider les données des memstores en exécutant le script [flush_all_tables.sh](https://github.com/Azure/hbase-utils/blob/master/scripts/flush_all_tables.sh) à partir du [dépôt GitHub hbase-utils](https://github.com/Azure/hbase-utils/).
   
   Vous pouvez aussi vider les données des memstores en exécutant la commande shell HBase suivante à partir du cluster HDInsight :
   
   ```bash
   hbase shell
   flush "<table-name>"
   ```
   
1. Connectez-vous à [Apache Ambari](https://ambari.apache.org/) sur le cluster source avec `https://<OLDCLUSTERNAME>.azurehdinsight.net`, puis arrêtez les services HBase.
   
1. À l’invite de confirmation, cochez la case pour activer le mode de maintenance pour HBase.
   
   Pour plus d’informations sur la connexion à Ambari et sur l’utilisation d’Ambari, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](../hdinsight-hadoop-manage-ambari.md).
   
1. Si votre cluster HBase source ne dispose pas de la fonctionnalité d’[écritures accélérées](apache-hbase-accelerated-writes.md), passez cette étape. Pour les clusters HBase sources qui utilisent les écritures accélérées, sauvegardez le répertoire du journal WAL sous HDFS en exécutant les commandes suivantes à partir d’une session SSH sur un nœud Zookeeper ou un nœud Worker du cluster source.
   
   ```bash
   hdfs dfs -mkdir /hbase-wal-backup
   hdfs dfs -cp hdfs://mycluster/hbasewal /hbase-wal-backup
   ```

### <a name="prepare-the-destination-cluster"></a>Préparer le cluster de destination

1. Sur le portail Azure, [configurez un nouveau cluster HDInsight de destination](../hdinsight-hadoop-provision-linux-clusters.md) qui utilise un compte de stockage différent de celui de votre cluster source.
   
1. Connectez-vous à [Apache Ambari](https://ambari.apache.org/) sur le nouveau cluster à l’adresse `https://<NEWCLUSTERNAME>.azurehdinsight.net`, puis arrêtez les services HBase.
   
1. Nettoyez les données Zookeeper sur le cluster de destination en exécutant les commandes suivantes dans un nœud Zookeeper ou worker :
   
   ```bash
   hbase zkcli
   rmr /hbase-unsecure
   quit
   ```
   
1. Faites basculer l’utilisateur vers HBase en exécutant `sudo su hbase`.

### <a name="clean-and-migrate-the-file-system-and-wal"></a>Nettoyer et migrer le système de fichiers et le journal WAL

Exécutez les commandes suivantes en fonction de votre version de HDI source et selon que les clusters source et de destination utilisent ou non les écritures accélérées. Le cluster de destination utilise toujours HDI version 4.0, car HDI 3.6 est en Support De base et n’est pas recommandé pour les nouveaux clusters.

- [Le cluster source utilise HDI 3.6 avec les écritures accélérées et le cluster de destination utilise les écritures accélérées](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination utilise des écritures accélérées](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées](#the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).
- [Le cluster source utilise HDI 4.0 avec les écritures accélérées et le cluster de destination utilise les écritures accélérées](#the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination utilise les écritures accélérées](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes).
- [Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées](#the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes).

Le `<container-endpoint-url>` du compte de stockage est `https://<storageaccount>.blob.core.windows.net/<container-name>`. Passez le jeton SAS du compte de stockage à la fin de l’URL.

- Le `<container-fullpath>` pour le stockage de type WASB est `wasbs://<container-name>@<storageaccount>.blob.core.windows.net`
- Le `<container-fullpath>` pour le stockage de type Azure Data Lake Storage Gen2 est `abfs://<container-name>@<storageaccount>.dfs.core.windows.net`.

#### <a name="copy-commands"></a>Commandes de copie

La commande de copie HDFS est `hdfs dfs <copy properties starting with -D> -cp`
 
Pour bénéficier de meilleures performances lors de la copie de fichiers qui ne se trouvent pas dans un objet blob de pages, utilisez `hadoop distcp` : `hadoop distcp <copy properties starting with -D>`
 
Pour passer la clé du compte de stockage, utilisez :
- `-Dfs.azure.account.key.<storageaccount>.blob.core.windows.net='<storage account key>'`
- `-Dfs.azure.account.keyprovider.<storageaccount>.blob.core.windows.net=org.apache.hadoop.fs.azure.SimpleKeyProvider`

Vous pouvez aussi utiliser [AzCopy](../../storage/common/storage-ref-azcopy.md) pour bénéficier de meilleures performances lors de la copie de fichiers de données HBase.
   
1. Exécutez la commande AzCopy :
   
   ```bash
   azcopy cp "<source-container-endpoint-url>/hbase" "<target-container-endpoint-url>" --recursive
   ```

1. Si le compte de stockage de destination est un stockage d’objets blob Azure, effectuez cette étape après la copie. Si le compte de stockage de destination est Data Lake Storage Gen2, ignorez cette étape.
   
   Le pilote Hadoop WASB utilise des objets blob spéciaux de taille 0 correspondant à chaque répertoire. AzCopy ignore ces fichiers pendant la copie. Sachant que certaines opérations WASB utilisent ces objets blob, vous devez les créer dans le cluster de destination. Pour créer les objets blob, exécutez la commande Hadoop suivante à partir d’un nœud du cluster de destination :
   
   ```bash
   sudo -u hbase hadoop fs -chmod -R 0755 /hbase
   ```

Vous pouvez télécharger AzCopy à partir de la page [Bien démarrer avec AzCopy](../../storage/common/storage-use-azcopy-v10.md). Pour plus d’informations sur l’utilisation d’AzCopy, consultez [azcopy copy](../../storage/common/storage-ref-azcopy-copy.md).

#### <a name="the-source-cluster-is-hdi-36-or-hdi-40-with-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>Le cluster source utilise HDI 3.6 ou HDI 4.0 avec les écritures accélérées et le cluster de destination utilise les écritures accélérées

1. Pour nettoyer le système de fichiers et migrer des données, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. Supprimez `hbase.id` en exécutant `hdfs dfs -rm /hbase/hbase.id`
   
1. Pour nettoyer et migrer le journal WAL, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal
   hdfs dfs -cp <source-container-fullpath>/hbase-wal-backup/hbasewal hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination utilise les écritures accélérées

1. Pour nettoyer le système de fichiers et migrer des données, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase /
   hdfs dfs -rm -r /hbase/*WALs
   ```
   
1. Supprimez `hbase.id` en exécutant `hdfs dfs -rm /hbase/hbase.id`
   
1. Pour nettoyer et migrer le journal WAL, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase/*WALs hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-36-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>Le cluster source utilise HDI 3.6 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées

1. Pour nettoyer le système de fichiers et migrer des données, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase /
   hdfs dfs -rm -r /hbase/*WALs
   ```
   
1. Supprimez `hbase.id` en exécutant `hdfs dfs -rm /hbase/hbase.id`
   
1. Pour nettoyer et migrer le journal WAL, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r /hbase-wals/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase/WALs,/hbase/MasterProcWALs,/hbase/oldWALs,/hbase-wals" -cp <source-container-fullpath>/hbase/*WALs /hbase-wals
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-has-accelerated-writes"></a>Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination utilise les écritures accélérées

1. Pour nettoyer le système de fichiers et migrer des données, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. Supprimez `hbase.id` en exécutant `hdfs dfs -rm /hbase/hbase.id`
   
1. Pour nettoyer et migrer le journal WAL, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r hdfs://<destination-cluster>/hbasewal
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals hdfs://<destination-cluster>/hbasewal
   ```

#### <a name="the-source-cluster-is-hdi-40-without-accelerated-writes-and-the-destination-cluster-doesnt-have-accelerated-writes"></a>Le cluster source utilise HDI 4.0 sans les écritures accélérées et le cluster de destination n’utilise pas les écritures accélérées

1. Pour nettoyer le système de fichiers et migrer des données, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r /hbase 
   hadoop distcp <source-container-fullpath>/hbase /
   ```
   
1. Supprimez `hbase.id` en exécutant `hdfs dfs -rm /hbase/hbase.id`
   
1. Pour nettoyer et migrer le journal WAL, exécutez les commandes suivantes :
   
   ```bash
   hdfs dfs -rm -r /hbase-wals/*
   hdfs dfs -Dfs.azure.page.blob.dir="/hbase-wals" -cp <source-container-fullpath>/hbase-wals /
   ```

### <a name="complete-the-migration"></a>Effectuer la migration

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