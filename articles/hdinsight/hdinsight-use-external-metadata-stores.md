---
title: Utiliser des magasins de métadonnées externes - Azure HDInsight
description: Utilisez des magasins de métadonnées externes avec les clusters Azure HDInsight et suivez les bonnes pratiques.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79233525"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Utiliser des magasins de métadonnées externes dans Azure HDInsight

HDInsight vous permet de prendre le contrôle de vos données et métadonnées en déployant des bases de données de gestion et des solutions de métadonnées clés dans des magasins de données externes. Cette fonctionnalité est actuellement disponible pour le [metastore Apache Hive](#custom-metastore), le [metastore Apache Oozie](#apache-oozie-metastore) et la [base de données Apache Ambari](#custom-ambari-db).

Le metastore Apache Hive dans HDInsight est une partie essentielle de l’architecture d’Apache Hadoop. Un metastore est le référentiel central de schémas qui peut être utilisé par d’autres outils d’accès aux données volumineuses, comme Apache Spark, Interactive Query (LLAP), Presto ou Apache Pig. HDInsight utilise une base de données Azure SQL Database en tant que metastore Hive.

![Architecture du metastore Hive dans HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Il existe deux façons de configurer un metastore pour vos clusters HDInsight :

* [Metastore par défaut](#default-metastore)
* [Metastore personnalisé](#custom-metastore)

## <a name="default-metastore"></a>Metastore par défaut

Par défaut, HDInsight crée un metastore avec chaque type de cluster. Vous pouvez à la place spécifier un metastore personnalisé. Le metastore par défaut comprend les considérations suivantes :

* Pas de coût supplémentaire. HDInsight crée un metastore avec chaque type de cluster sans coût supplémentaire pour vous.

* Chaque metastore par défaut fait partie du cycle de vie du cluster. Lorsque vous supprimez un cluster, le metastore et les métadonnées correspondants sont également supprimés.

* Vous ne pouvez pas partager le metastore par défaut avec d’autres clusters.

* Le metastore par défaut utilise une base de données Azure SQL de base, qui a une limite de cinq DTU (unités de transaction de base de données).
Ce metastore par défaut est généralement utilisé pour les charges de travail relativement simples qui n’ont pas besoin de plusieurs clusters et ne nécessitent pas la conservation des données au-delà du cycle de vie du cluster.

## <a name="custom-metastore"></a>Metastore personnalisé

HDInsight prend également en charge les magasins de métadonnées personnalisés, qui sont recommandés pour les clusters de production :

* Vous spécifiez votre propre base de données Azure SQL en tant que metastore.

* Le cycle de vie du metastore n’est pas lié à un cycle de vie de cluster. Vous pouvez donc créer et supprimer des clusters sans perdre les métadonnées. Les métadonnées telles que vos schémas Hive sont conservées même une fois que vous supprimez et recréez le cluster HDInsight.

* Un metastore personnalisé vous permet d’attacher plusieurs clusters et types de cluster à ce metastore. Par exemple, un seul metastore peut être partagé entre les clusters de requête interactive, Hive et Spark dans HDInsight.

* Vous payez le coût d’un metastore (Azure SQL DB), selon le niveau de performance que vous choisissez.

* Vous pouvez augmenter l’échelle du metastore en fonction de vos besoins.

* Le cluster et le metastore externe doivent être hébergés dans la même région.

![Cas d’usage d’un metastore Hive dans HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Créer et configurer une base de données Azure SQL Database pour le metastore personnalisé

Vous devez créer ou avoir une base de données Azure SQL Database existante avant de configurer un metastore Hive personnalisé pour un cluster HDInsight.  Pour plus d’informations, consultez [Démarrage rapide : Créer une base de données unique dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal).

Pour vous assurer que votre cluster HDInsight pourra accéder à la base de données Azure SQL Database connectée, configurez les règles de pare-feu Azure SQL Database pour autoriser les services et ressources Azure à accéder au serveur.

Vous pouvez activer cette option dans le Portail Azure en cliquant sur **Définir le pare-feu du serveur** et sur **ON** sous **Autoriser les services et les ressources Azure à accéder à ce serveur** pour la base de données ou le serveur Azure SQL Database. Pour plus d’informations, consultez [Créer et gérer des règles de pare-feu IP](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

![bouton Définir le pare-feu du serveur](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![autoriser l’accès des services Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Sélectionner un metastore personnalisé lors de la création du cluster

Vous pouvez pointer votre cluster vers une base de données Azure SQL créée précédemment lors de la création du cluster, ou vous pouvez configurer la base de données SQL une fois que le cluster est créé. Cette option est spécifiée avec les paramètres **Stockage > Metastore** lors de la création d’un nouveau cluster Hadoop, Spark ou Hive interactif à partir du Portail Microsoft Azure.

![Metastore Hive dans HDInsight dans le portail Azure](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Meilleures pratiques pour les metastores Hive

Voici certaines des meilleures pratiques pour les metastores Hive dans HDInsight :

* Utilisez un metastore personnalisé aussi souvent que possible, afin de mieux séparer les ressources de calcul (votre cluster en cours d’exécution) et les métadonnées (stockées dans le metastore).

* Commencez avec un niveau S2, qui fournit 50 DTU et 250 Go de stockage. Si vous voyez un goulot d’étranglement, vous pouvez mettre à l’échelle la base de données.

* Si vous prévoyez d’utiliser plusieurs clusters HDInsight pour accéder aux données distinctes, utilisez une base de données distincte pour le metastore sur chaque cluster. Si vous partagez un metastore sur plusieurs clusters HDInsight, cela signifie que les clusters utilisent les mêmes métadonnées et fichiers de données utilisateur sous-jacentes.

* Sauvegardez régulièrement votre metastore personnalisé. Azure SQL Database génère automatiquement des sauvegardes, mais la période de conservation des sauvegardes varie. Pour plus d’informations, consultez [En savoir plus sur les sauvegardes automatiques SQL Database](../sql-database/sql-database-automated-backups.md).

* Localisez votre metastore et votre cluster HDInsight dans la même région, pour des performances optimales et des frais de sortie de réseau plus bas.

* Surveillez les performances et la disponibilité de votre metastore à l’aide des outils de surveillance d’Azure SQL Database, tels que le Portail Microsoft Azure ou les journaux Azure Monitor.

* Lorsqu’une nouvelle version d’Azure HDInsight plus élevée est créée sur une base de données de metastore personnalisée existante, le système met à niveau le schéma du metastore, qui est irréversible sans restaurer la base de données à partir de la sauvegarde.

* Si vous partagez un metastore sur plusieurs clusters, assurez-vous que tous les clusters utilisent la même version de HDInsight. Différentes versions d’Hive utilisent différents schémas de base de données de metastore. Par exemple, vous ne pouvez pas partager un metastore entre des clusters Hive de versions 2.1 et 3.1.

* Dans HDInsight 4.0, Spark et Hive utilisent des catalogues indépendants pour accéder aux tables SparkSQL ou Hive. Les tables créées par Spark résident dans le catalogue Spark. Les tables créées par Hive résident dans le catalogue Hive. Dans HDInsight 3.6, Hive et Spark partageaient le même catalogue. L’intégration de Hive et Spark à HDInsight 4.0 s’appuie sur le connecteur Hive Warehouse Connector (HWC). Le connecteur HWC sert de pont entre Spark et Hive. [En savoir plus sur le connecteur Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md)

## <a name="apache-oozie-metastore"></a>Metastore Apache Oozie

Apache Oozie est un système de coordination de flux de travail qui gère les tâches Hadoop.  Oozie prend en charge les tâches Hadoop pour Apache MapReduce, Pig, Hive et autres.  Oozie utilise un metastore pour stocker les détails sur les flux de travail en cours et terminés. Pour améliorer les performances avec Oozie, utilisez une base de données Azure SQL en tant que magasin de metastore personnalisé. Le metastore permet également d’accéder aux données de travail Oozie après la suppression de votre cluster.

Pour obtenir des instructions sur la création d’un metastore Oozie avec Azure SQL Database, consultez [Utiliser Apache Oozie pour les flux de travail](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Base de données Ambari personnalisée

Pour utiliser votre propre base de données externe avec Apache Ambari sur HDInsight, consultez [Base de données Apache Ambari personnalisée](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des clusters dans HDInsight avec Apache Hadoop, Apache Spark, Apache Kafka, etc.](./hdinsight-hadoop-provision-linux-clusters.md)
