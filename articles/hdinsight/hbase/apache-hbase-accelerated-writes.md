---
title: Azure HDInsight accélérée des écritures pour Apache HBase
description: Donne une vue d’ensemble de la fonctionnalité d’Azure HDInsight accélérée écrit, qui utilise des disques gérés premium pour améliorer les performances Apache HBase écrire directement du journal de.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: f9314355d3dce8d96b794cdb81ab9c3d77faab45
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159299"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight accélérée des écritures pour Apache HBase

Cet article fournit en arrière-plan sur le **accélérée écrit** fonctionnalité pour Apache HBase dans Azure HDInsight et comment elle peut être utilisée efficacement pour améliorer les performances en écriture. **Accélération des écritures** utilise [disques gérés Azure premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) pour améliorer les performances de l’Apache HBase écrire à l’avance journal WAL (Write). Pour en savoir plus sur Apache HBase, consultez [What ' s Apache HBase dans HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Vue d’ensemble de l’architecture de HBase

Dans HBase, une **ligne** se compose d’une ou plusieurs **colonnes** et est identifiée par un **clé de ligne**. Plusieurs lignes constituent un **table**. Les colonnes contiennent **cellules**, qui sont des versions horodaté de la valeur dans cette colonne. Les colonnes sont regroupées en **familles de colonnes**, et toutes les colonnes dans une famille de colonnes sont stockées dans le fichier de stockage appelé **HFiles**.

**Régions** dans HBase sont utilisés pour équilibrer la charge de traitement des données. Tout d’abord, HBase stocke les lignes d’une table dans une seule région. Les lignes sont réparties sur plusieurs régions en tant que la quantité de données dans la table est élevé. **Serveurs de région** peut gérer les demandes pour plusieurs régions.

## <a name="write-ahead-log-for-apache-hbase"></a>Write Ahead Log pour Apache HBase

HBase écrit d’abord mises à jour des données à un type de journal de validation appelé un écrire à l’avance journal WAL (Write). Une fois la mise à jour est stockée dans le journal WAL, il est écrit dans la mémoire **MemStore**. Lorsque les données dans la mémoire atteint sa capacité maximale, il a écrit sur le disque en tant qu’un **HFile**.

Si un **RegionServer** se bloque ou devient indisponible avant que le MemStore est vidé, le journal écrire à l’avance peut être utilisé pour relire les mises à jour. Sans le journal WAL, si un **RegionServer** tombe en panne avant de vider les mises à jour à un **HFile**, toutes ces mises à jour sont perdues.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Fonctionnalité d’écritures accélérée dans HDInsight Azure pour Apache HBase

La fonctionnalité accélérée écrit résout le problème d’écriture-latences supérieures dû écrire à l’avance les journaux qui se trouvent dans le stockage cloud.  La fonctionnalité d’accélération écrit pour HDInsight Apache HBase clusters, attache les disques SSD gérés premium pour chaque RegionServer (nœud worker). Écrire à l’avance les journaux sont ensuite écrites pour le fichier système HDFS (Hadoop) monté sur ces disques gérés premium au lieu de stockage cloud.  Disques gérés Premium utilisent des disques SSD (SSD) et offrent d’excellentes performances d’e/s avec une tolérance de panne.  Contrairement aux disques non gérés, si une unité de stockage tombe en panne, il n’affecte pas les autres unités de stockage dans le même groupe à haute disponibilité.  Par conséquent, des disques gérés fournissent écriture-latence faible et une meilleure résilience pour vos applications. Pour en savoir plus sur les disques gérés par Azure, consultez [Introduction à Azure des disques gérés](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Comment activer l’accélération écrit pour HBase dans HDInsight

Pour créer un nouveau cluster HBase avec la fonctionnalité d’accélération écrit, suivez les étapes de [configurer des clusters dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) jusqu'à ce que vous atteigniez **étape 3, le stockage**. Sous **paramètres du Metastore**, cliquez sur la case à cocher en regard **activer accélérée écrit (version préliminaire)**. Ensuite, passez aux étapes restantes pour la création du cluster.

![Activer l’option écritures accélérée pour HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Autres points à considérer

Pour conserver la durabilité des données, créer un cluster avec un minimum de trois nœuds de travail. Une fois créé, vous ne pouvez pas descendre en puissance le cluster à moins de trois nœuds de travail.

Ou de désactiver vos tables HBase avant de supprimer le cluster, afin que vous ne perdez pas les données de journal des écritures à l’avance.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Étapes suivantes

* Documentation officielle de Apache HBase sur le [fonctionnalité d’écrire les journaux à l’avance](https://hbase.apache.org/book.html#wal)
* Pour mettre à niveau votre cluster HDInsight Apache HBase pour utiliser l’accélération écrit, consultez [migrer un cluster Apache HBase vers une nouvelle version](apache-hbase-migrate-new-version.md).
