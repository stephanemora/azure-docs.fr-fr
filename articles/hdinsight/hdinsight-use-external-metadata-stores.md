---
title: Utiliser des magasins de métadonnées externes - Azure HDInsight
description: Utilisez des magasins de métadonnées externes avec les clusters HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 3daa71c91d1e49a497a979b9b5b89df1fcb9418c
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889679"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Utiliser des magasins de métadonnées externes dans Azure HDInsight

Le metastore Apache Hive dans HDInsight est une partie essentielle de l’architecture d’Apache Hadoop. Un metastore est le référentiel central de schémas qui peut être utilisé par d’autres outils d’accès aux données volumineuses, comme Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. HDInsight utilise une base de données Azure SQL Database en tant que metastore Hive.

![Architecture du metastore Hive dans HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Il existe deux façons de configurer un metastore pour vos clusters HDInsight :

* [Metastore par défaut](#default-metastore)
* [Metastore personnalisé](#custom-metastore)

## <a name="default-metastore"></a>Metastore par défaut

Par défaut, HDInsight crée un metastore avec chaque type de cluster. Vous pouvez à la place spécifier un metastore personnalisé. Le metastore par défaut comprend les considérations suivantes :
- Pas de coût supplémentaire. HDInsight crée un metastore avec chaque type de cluster sans coût supplémentaire pour vous.
- Chaque metastore par défaut fait partie du cycle de vie du cluster. Lorsque vous supprimez un cluster, le metastore et les métadonnées correspondants sont également supprimés.
- Vous ne pouvez pas partager le metastore par défaut avec d’autres clusters.
- Le metastore par défaut utilise une base de données Azure SQL de base, qui a une limite de cinq DTU (unités de transaction de base de données).
Ce metastore par défaut est généralement utilisé pour les charges de travail relativement simples qui n’ont pas besoin de plusieurs clusters et ne nécessitent pas la conservation des données au-delà du cycle de vie du cluster.


## <a name="custom-metastore"></a>Metastore personnalisé

HDInsight prend également en charge les magasins de métadonnées personnalisés, qui sont recommandés pour les clusters de production :
- Vous spécifiez votre propre base de données SQL Azure en tant que metastore.
- Le cycle de vie du metastore n’est pas lié à un cycle de vie de cluster, vous pouvez donc créer et supprimer des clusters sans perdre les métadonnées. Les métadonnées telles que vos schémas Hive sont conservées même une fois que vous supprimez et recréez le cluster HDInsight.
- Un metastore personnalisé vous permet d’attacher plusieurs clusters et types de cluster à ce metastore. Par exemple, un seul metastore peut être partagé entre les clusters de requête interactive, Hive et Spark dans HDInsight.
- Vous payez le coût d’un metastore (Azure SQL DB), selon le niveau de performance que vous choisissez.
- Vous pouvez augmenter l’échelle du metastore en fonction de vos besoins.

![Cas d’usage d’un metastore Hive dans HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Sélectionner un metastore personnalisé lors de la création du cluster

Vous pouvez pointer votre cluster vers une base de données Azure SQL créée précédemment lors de la création du cluster, ou vous pouvez configurer la base de données SQL une fois que le cluster est créé. Cette option est spécifiée avec les paramètres Stockage > Metastore lors de la création d’un nouveau cluster Hadoop, Spark ou requête interactive à partir du portail Azure.

![Metastore Hive dans HDInsight dans le portail Azure](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Vous pouvez également ajouter des clusters supplémentaires à un metastore personnalisé à partir du portail Azure ou des configurations Ambari (Hive > Avancé)

![Metastore Hive dans HDInsight Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Meilleures pratiques pour les metastores Hive

Voici certaines des meilleures pratiques pour les metastores Hive dans HDInsight :

- Utilisez un metastore personnalisé aussi souvent que possible, afin de mieux séparer les ressources de calcul (votre cluster en cours d’exécution) et les métadonnées (stockées dans le metastore).
- Commencez avec un niveau S2, qui fournit 50 DTU et 250 Go de stockage. Si vous voyez un goulot d’étranglement, vous pouvez mettre à l’échelle la base de données.
- Si vous prévoyez d’utiliser plusieurs clusters HDInsight pour accéder aux données distinctes, utilisez une base de données distincte pour le metastore sur chaque cluster. Si vous partagez un metastore sur plusieurs clusters HDInsight, cela signifie que les clusters utilisent les mêmes métadonnées et fichiers de données utilisateur sous-jacentes.
- Sauvegardez régulièrement votre metastore personnalisé. Azure SQL Database génère automatiquement des sauvegardes, mais la période de rétention des sauvegardes varie. Pour plus d’informations, consultez [En savoir plus sur les sauvegardes automatiques SQL Database](../sql-database/sql-database-automated-backups.md).
- Localisez votre metastore et votre cluster HDInsight dans la même région, pour des performances optimales et des frais de sortie de réseau plus bas.
- Surveiller les performances et la disponibilité à l’aide des outils de surveillance de base de données SQL Azure, tels que le portail Azure ou les journaux d’Azure Monitor de votre metastore.
- Lorsqu’une nouvelle version d’Azure HDInsight plus élevée est créée sur une base de données de metastore personnalisée existante, le système met à niveau le schéma du metastore, qui est irréversible sans restaurer la base de données à partir de la sauvegarde.
- Si vous partagez un metastore sur plusieurs clusters, assurez-vous que tous les clusters utilisent la même version de HDInsight. Différentes versions d’Hive utilisent différents schémas de base de données de metastore. Par exemple, vous ne pouvez pas partager un metastore sur les clusters Hive versions 1.2 et 2.1. 

##  <a name="apache-oozie-metastore"></a>Metastore Apache Oozie

Apache Oozie est un système de coordination de flux de travail qui gère les tâches Hadoop.  Oozie prend en charge les tâches Hadoop pour Apache MapReduce, Pig, Hive et autres.  Oozie utilise un metastore pour stocker les détails sur les flux de travail en cours et terminés. Pour améliorer les performances avec Oozie, utilisez une base de données Azure SQL en tant que magasin de metastore personnalisé. Le metastore permet également d’accéder aux données de travail Oozie après la suppression de votre cluster.

Pour obtenir des instructions sur la création d’un metastore Oozie avec Azure SQL Database, consultez [Utiliser Apache Oozie pour les flux de travail](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Étapes suivantes

- [Configurer des clusters dans HDInsight avec Apache Hadoop, Apache Spark, Apache Kafka, etc.](./hdinsight-hadoop-provision-linux-clusters.md)
