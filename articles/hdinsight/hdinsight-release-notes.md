---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils de développement et les détails pour Hadoop, Spark, R Server, Hive et bien plus encore.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 0beac64ceb0bbf729d2f6d0f6a0ca8d5af499f42
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725417"
---
# <a name="release-notes-for-azure-hdinsight"></a>Notes de publication pour Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [l’article sur le contrôle de version de HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l'un des services les plus populaires parmi les clients d'entreprise pour l'analytique Apache Hadoop et Apache Spark open source sur Azure.

## <a name="new-features"></a>Nouvelles fonctionnalités

Pour plus d’informations sur les modifications importantes avec HDInsight 4.0., consultez [quelles sont les nouveautés dans HDI 4.0 ?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Versions des composants

Les versions Apache officielles de tous les composants de HDInsight 4.0 sont fournis ci-dessous. Les composants répertoriés sont les versions de la version stable la plus récente disponible.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- Apache HBase 2.0.0
- Apache Hive 3.1.0
- Apache Kafka 1.1.1
- Apache Mahout 0.9.0+
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache Pig 0.16.0
- Apache Ranger 0.7.0
- Apache Slider 0.92.0
- Apache Spark 2.3.1
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Les versions ultérieures des composants Apache sont parfois regroupées dans la distribution de HDP en plus des versions répertoriées ci-dessus. Dans ce cas, ces versions sont répertoriées dans le tableau des préversions techniques et ne doivent pas remplacer les versions des composants Apache de la liste ci-dessus dans un environnement de production.

## <a name="apache-patch-information"></a>Informations sur les correctifs Apache

Pour plus d’informations sur les correctifs disponibles dans HDInsight 4.0, consultez le correctif logiciel pour chaque produit dans le tableau ci-dessous.

| Nom du produit | Informations sur le correctif |
|---|---|
| Ambari | [Informations sur le correctif Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informations sur le correctif Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| hbase | [Informations sur le correctif HBase](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Cette version offre Hive 3.1.0 avec aucun correctif Apache supplémentaires.  |
| Kafka | Cette version propose Kafka 1.1.1 avec aucun correctif Apache supplémentaires. |
| Oozie | [Informations sur le correctif Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Informations sur le correctif Phoenix](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informations sur le correctif pig](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informations sur le correctif ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informations sur le correctif Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Cette version propose Sqoop 1.4.7 avec aucun correctif Apache supplémentaires. |
| Tez | Cette version offre Tez 0.9.1 avec aucun correctif Apache supplémentaires. |
| Zeppelin | Cette version offre Zeppelin 0.8.0 avec aucun correctif Apache supplémentaires. |
| Zookeeper | [Informations sur le correctif zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Failles et menaces courantes corrigées

Pour plus d’informations sur la sécurité des problèmes résolus dans cette version, consultez 'Hortonworks [fixe Common Vulnerabilities and Exposures pour HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Problèmes connus

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>La réplication est interrompue pour sécuriser de HBase avec l’installation par défaut

Pour HDInsight 4.0, procédez comme suit :

1. Activer les communications entre les clusters.
1. Connectez-vous au nœud principal actif.
1. Télécharger un script pour activer la réplication avec la commande suivante :

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Tapez la commande `sudo kinit <domainuser>`.
1. Tapez la commande suivante pour exécuter le script :

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
Pour HDInsight 3.6, procédez comme suit :

1. Connectez-vous à active ZK HMaster.
1. Télécharger un script pour activer la réplication avec la commande suivante :
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Tapez la commande `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Tapez la commande suivante :

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix Sqlline cesse de fonctionner après la migration de cluster HBase HDInsight 4.0

Effectuez également les étapes suivantes :

1. Supprimer les tables de Phoenix suivantes :
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Si vous ne pouvez pas supprimer une table, redémarrez HBase pour effacer toutes les connexions aux tables.
1. Exécutez de nouveau `sqlline.py`. Phoenix recréera toutes les tables qui ont été supprimées à l’étape 1.
1. Régénérer les tables Phoenix et les vues de vos données de HBase.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix Sqlline cesse de fonctionner après la réplication des métadonnées Phoenix de HBase à partir de HDInsight 3.6 à 4.0

Effectuez également les étapes suivantes :

1. Avant de procéder à la réplication, le cluster de destination 4.0 et exécutez `sqlline.py`. Cette commande génère des tables Phoenix telles que `SYSTEM.MUTEX` et `SYSTEM.LOG` qui existent uniquement dans 4.0.
1. Supprimer les tables suivantes :
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Démarrer la réplication HBase

## <a name="deprecation"></a>Dépréciation

Apache Storm et ML services ne sont pas disponibles dans HDInsight 4.0.
