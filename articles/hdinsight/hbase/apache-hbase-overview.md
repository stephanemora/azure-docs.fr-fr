---
title: Qu’est-ce qu’Azure HDBase dans Azure HDInsight ?
description: Présentation d’Apache HBase dans HDInsight, une base de données NoSQL reposant sur Hadoop. En savoir plus sur les cas d'utilisation et la comparaison de HBase sur d'autres clusters Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271850"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Qu’est-ce qu’Azure HDBase dans Azure HDInsight

[Apache HBase](https://hbase.apache.org/) est une base de données NoSQL open source, basée sur [Apache Hadoop](https://hadoop.apache.org/) et modélisée d’après [Google BigTable](https://cloud.google.com/bigtable/). HBase fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées, dans une base de données sans schéma, organisée par familles de colonnes.

Du point de vue de l’utilisateur, HBase est similaire à une base de données. Les données sont stockées dans les lignes et colonnes d’une table et les données au sein d’une ligne sont regroupées par familles de colonnes. HBase est une base de données sans schéma dans le sens où ni les colonnes ni le type de données qui y sont stockées ne doivent être définis avant de pouvoir les utiliser. Le code open source peut être mis à l'échelle de façon linéaire pour gérer des pétaoctets de données dans des milliers de nœuds. Il peut reposer sur la redondance des données, le traitement par lots et d'autres fonctionnalités qui sont fournies par des applications distribuées dans l'écosystème Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Implémentation d’Apache HBase dans Azure HDInsight

HDInsight HBase est proposé en tant que cluster géré intégré à l'environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le [Stockage Azure](./../hdinsight-hadoop-use-blob-storage.md), ce qui permet d’obtenir une latence faible et une plus grande souplesse dans le choix des performances et des coûts. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop. HBase et Hadoop sont de bons points de départ pour les projets présentant des données volumineuses dans Azure, et, en particulier, ils permettent à des applications en temps réel d'opérer avec des jeux de données volumineux.

La mise en œuvre de HDInsight exploite l'architecture de montée en charge de HBase pour fournir un partitionnement automatique des tables, une cohérence forte pour les lectures et les écritures, et un basculement automatique. Les performances sont optimisées par la mise en cache en mémoire des lectures et par des écritures en diffusion à débit élevé. Vous pouvez créer un cluster HBase au sein du réseau virtuel. Pour en savoir plus, voir [Création de clusters HBase sur Azure Virtual Network](./apache-hbase-provision-vnet.md).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Mode de gestion des données HDInsight HBase

Les données peuvent être gérées dans HBase au moyen des commandes `create`, `get`, `put` et `scan` provenant du Shell HBase. Les données sont écrites dans la base de données au moyen de `put` et lues avec `get`. La commande `scan` permet d'obtenir des données à partir de plusieurs lignes dans une table. Les données peuvent également être gérées au moyen de l'API HBase C#, qui fournit une bibliothèque cliente par-dessus l'API REST HBase. Une base de données HBase peut également être interrogée en utilisant [Apache Hive](https://hive.apache.org/). En guise d’introduction à ces modèles de programmation, consultez la page [Bien démarrer avec Apache HBase et Apache Hadoop dans HDInsight](./apache-hbase-tutorial-get-started-linux.md). Des coprocesseurs, permettant de traiter des données dans les nœuds qui hébergent la base de données, sont également disponibles.

> [!NOTE]  
> Thrift n’est pas pris en charge par HBase dans HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Cas d’utilisation pour Apache HBase

Les recherches Web sont le cas d’utilisation canonique pour lequel BigTable, et par extension HBase, ont été créés. Les moteurs de recherche créent des index qui mappent les termes avec les pages web les contenant. Mais il existe de nombreux autres cas d'utilisation pour lesquels HBase est adapté : plusieurs sont répertoriés dans cette section.

|Scénario |Description |
|---|---|
|stockage clé-valeur|HBase peut être utilisé comme stockage de paires clé-valeur et convient pour la gestion des systèmes de messagerie. Facebook utilise HBase pour son système de messagerie, idéal pour le stockage et la gestion des communications Internet. WebTable utilise HBase pour rechercher et gérer des tables extraites à partir de pages Web.|
|données de capteur|HBase est utile pour la capture de données collectées de façon incrémentielle à partir de diverses sources. Cela inclut l’analytique des réseaux sociaux, les séries chronologiques, l’actualisation des tableaux de bord interactifs avec les tendances et les compteurs, et la gestion des systèmes de journaux d’audit. Par exemple, le Bloomberg Terminal et la base de données OpenTSDB (Open Time Series Database) qui stocke les métriques collectées sur l'intégrité des systèmes serveur et y donne accès.|
|requête en temps réel|[Apache Phoenix](https://phoenix.apache.org/) est un moteur de requête SQL pour Apache HBase. Il est accessible en tant que pilote JDBC, et permet d’interroger et de gérer les tables HBase au moyen de SQL.|
|HBase en tant que plateforme|Les applications peuvent fonctionner par-dessus HBase, en l'utilisant comme banque de données. Exemples : Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji et Titan. Les applications peuvent également s'intégrer à HBase. Exemples : [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [Solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [Ganglia](http://ganglia.info/) et [Apache Drill](https://drill.apache.org/).|

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec l’utilisation d’Apache HBase et d’Apache Hadoop dans HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Création de clusters HBase sur Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Configurer la réplication Apache HBase dans HDInsight](apache-hbase-replication.md)
