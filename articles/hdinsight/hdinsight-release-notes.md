---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 7c31520efd881e8e0b5ed309f62d273bac59c0e3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945028"
---
# <a name="release-notes"></a>Notes de publication

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [l’article sur le contrôle de version de HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l'un des services les plus populaires parmi les clients d'entreprise pour l'analytique Apache Hadoop et Apache Spark open source sur Azure.

## <a name="new-features"></a>Nouvelles fonctionnalités

Pour plus d’informations sur les modifications importantes liées à HDInsight 4.0., consultez [Vue d’ensemble de HDInsight 4.0](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Versions des composants

Les versions Apache officielles de tous les composants HDInsight 4.0 sont indiquées ci-dessous. Les versions des composants répertoriés sont les versions stables les plus récentes disponibles.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Les versions ultérieures des composants Apache sont parfois groupées dans la distribution de la plateforme HDP en plus des versions répertoriées ci-dessus. Dans ce cas, ces versions sont répertoriées dans le tableau des préversions techniques et ne doivent pas remplacer les versions des composants Apache de la liste ci-dessus dans un environnement de production.

## <a name="apache-patch-information"></a>Informations sur les correctifs Apache

Pour plus d’informations sur les correctifs disponibles dans HDInsight 4.0, consultez la liste des correctifs de chaque produit dans le tableau ci-dessous.

| Nom du produit | Informations sur les correctifs |
|---|---|
| Ambari | [Informations sur les correctifs Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informations sur les correctifs Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| hbase | [Informations sur les correctifs HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Cette version fournit Hive 3.1.0 sans aucun correctif Apache supplémentaire.  |
| Kafka | Cette version fournit Kafka 1.1.1 sans aucun correctif Apache supplémentaire. |
| Oozie | [Informations sur les correctifs Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informations sur les correctifs Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informations sur les correctifs Pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informations sur les correctifs Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informations sur les correctifs Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Cette version fournit Sqoop 1.4.7 sans aucun correctif Apache supplémentaire. |
| Tez | Cette version fournit Tez 0.9.1 sans aucun correctif Apache supplémentaire. |
| Zeppelin | Cette version fournit Zeppelin 0.8.0 sans aucun correctif Apache supplémentaire. |
| Zookeeper | [Informations sur les correctifs Zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Failles et menaces courantes corrigées

Pour plus d’informations sur les problèmes de sécurité résolus dans cette version, consultez le document [Fixed Common Vulnerabilities and Exposures for HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html) (Failles et menaces courantes corrigées pour HDP 3.0.1) sur la plateforme Hortonworks.

## <a name="known-issues"></a>Problèmes connus

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>Réplication endommagée pour la base de données HBase sécurisée avec l’installation par défaut

Pour HDInsight 4.0, procédez comme suit :

1. Activez la communication entre les clusters.
1. Connectez-vous au nœud principal actif.
1. Téléchargez un script pour activer la réplication avec la commande suivante :

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Tapez la commande `sudo kinit <domainuser>`.
1. Tapez la commande suivante pour exécuter le script :

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Pour HDInsight 3.6, procédez comme suit :

1. Connectez-vous à l’instance HMaster ZK active.
1. Téléchargez un script pour activer la réplication avec la commande suivante :
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Tapez la commande `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Tapez la commande suivante :

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline cesse de fonctionner après la migration du cluster HBase vers HDInsight 4.0

Effectuez également les étapes suivantes :

1. Supprimez les tables Phoenix suivantes :
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Si vous ne pouvez pas supprimer une table, redémarrez HBase pour effacer les connexions aux tables.
1. Exécutez de nouveau `sqlline.py`. Phoenix va recréer toutes les tables qui ont été supprimées à l’étape 1.
1. Régénérez les tables et les vues Phoenix des données HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline cesse de fonctionner après la réplication des métadonnées HBase Phoenix de HDInsight 3.6 à 4.0

Effectuez également les étapes suivantes :

1. Avant de procéder à la réplication, accédez au cluster de destination 4.0 et exécutez `sqlline.py`. Cette commande génère des tables Phoenix telles que `SYSTEM.MUTEX` et `SYSTEM.LOG` qui existent uniquement dans 4.0.
1. Supprimez les tables suivantes :
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Démarrez la réplication HBase.

## <a name="deprecation"></a>Dépréciation

Apache Storm et les services ML ne sont pas disponibles dans HDInsight 4.0.
