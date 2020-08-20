---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: 82a66d88b3c7c3492e7cb90116ff8ff8e61ca1b0
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88036587"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

## <a name="release-date-08092020"></a>Date de publication : 09/08/2020

Cette version s’applique uniquement à HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="support-for-sparkcruise"></a>Prise en charge de SparkCruise
SparkCruise est un système de réutilisation automatique de calcul pour Spark. Il sélectionne les sous-expressions communes à matérialiser en fonction de la charge de travail des requêtes passées. SparkCruise matérialise ces sous-expressions dans le cadre du traitement des requêtes, et la réutilisation du calcul est automatiquement appliquée en arrière-plan. Vous pouvez tirer parti de SparkCruise sans aucune modification du code Spark.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Prise en charge de la vue Hive pour HDInsight 4.0
La vue Apache Ambari Hive est conçue pour vous aider à créer, optimiser et exécuter des requêtes Hive à partir de votre navigateur web. La vue Hive est prise en charge en mode natif pour les clusters HDInsight 4.0 à compter de cette version. Elle ne s’applique pas aux clusters existants. Vous devez supprimer et recréer le cluster pour obtenir la vue Hive intégrée.
 
### <a name="support-tez-view-for-hdinsight-40"></a>Prise en charge de la vue Tez pour HDInsight 4.0
La vue Apache Tez est utilisée pour suivre et déboguer l’exécution de la tâche Hive Tez. La vue Tez est prise en charge en mode natif pour HDInsight 4.0 à compter de cette version. Elle ne s’applique pas aux clusters existants. Vous devez supprimer et recréer le cluster pour obtenir la vue Tez intégrée.

## <a name="deprecation"></a>Dépréciation
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Dépréciation de Spark 2.1 et 2.2 dans le cluster Spark HDInsight 3.6
À compter du 1er juillet 2020, les clients ne peuvent pas créer des clusters Spark avec Spark 2.1 et 2.2 sur HDInsight 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Spark 2.3 sur HDInsight 3.6 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Dépréciation de Spark 2.3 dans le cluster Spark HDInsight 4.0
À compter du 1er juillet 2020, les clients ne peuvent pas créer des clusters Spark avec Spark 2.3 sur HDInsight 4.0. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Spark 2.4 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Désapprobation de Kafka 1.1 dans le cluster Kafka HDInsight 4.0
À compter du 1er juillet 2020, les clients ne seront pas en mesure de créer de nouveaux clusters Kafka avec Kafka 1.1 sur HDInsight 4.0. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Kafka 2.1 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.

## <a name="behavior-changes"></a>Changements de comportement
### <a name="ambari-stack-version-change"></a>Modification de la version de la pile Ambari
À partir de cette version, la version d’Ambari est passée de 2.x.x.x à 4.1. Vous pouvez obtenir la version d’Ambari à partir de l’interface d’Ambari > À propos.

## <a name="upcoming-changes"></a>Changements à venir
Aucun changement cassant à venir auquel vous devez prêter attention.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

Les JIRA ci-dessous sont portés pour Hive :
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

Les JIRA ci-dessous sont portés pour HBase :
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>Changement de la version des composants
Aucune modification de la version des composants pour cette version. Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).