---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 0481f92666c8ca547dc427e117f9917afb257629
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86511872"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

## <a name="release-date-07132020"></a>Date de publication : 13/07/2020

Cette version s’applique à la fois à HDInsight 3.6 et 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Support pour Customer Lockbox pour Microsoft Azure
Azure HDInsight prend maintenant en charge Azure Customer Lockbox. Il offre une interface permettant aux clients de passer en revue et d'approuver ou de rejeter les demandes d'accès aux données des clients. Il est utilisé lorsqu’un ingénieur Microsoft doit accéder aux données client dans le cadre d’une demande de support. Pour plus d’informations, consultez [Customer Lockbox pour Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview).

### <a name="service-endpoint-policies-for-storage"></a>Stratégies de points de terminaison de service pour le stockage
Les clients peuvent maintenant utiliser des stratégies de point de terminaison de service sur le sous-réseau de cluster HDInsight. En savoir plus sur la [stratégie de point de terminaison de service Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Dépréciation
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Dépréciation de Spark 2.1 et 2.2 dans le cluster Spark HDInsight 3.6
À compter du 1er juillet 2020, les clients ne peuvent pas créer des clusters Spark avec Spark 2.1 et 2.2 sur HDInsight 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Spark 2.3 sur HDInsight 3.6 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Dépréciation de Spark 2.3 dans le cluster Spark HDInsight 4.0
À compter du 1er juillet 2020, les clients ne peuvent pas créer des clusters Spark avec Spark 2.3 sur HDInsight 4.0. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Spark 2.4 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Désapprobation de Kafka 1.1 dans le cluster Kafka HDInsight 4.0
À compter du 1er juillet 2020, les clients ne seront pas en mesure de créer de nouveaux clusters Kafka avec Kafka 1.1 sur HDInsight 4.0. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Kafka 2.1 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.

## <a name="behavior-changes"></a>Changements de comportement
Aucun changement de comportement auquel vous devez être attentif.

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Possibilité de sélectionner différentes références SKU Zookeeper pour les services Spark, Hadoop et ML
HDInsight ne prend actuellement pas en charge la modification de la référence SKU Zookeeper pour les types de cluster des services Spark, Hadoop et ML. Il utilise la référence SKU A2_v2/A2 pour les nœuds Zookeeper et ceux-ci ne sont pas facturés aux clients. Dans la prochaine version, les clients pourront modifier la référence SKU Zookeeper pour les services Spark, Hadoop et ML en fonction des besoins. Les nœuds Zookeeper avec une référence différente de A2_v2/A2 sont facturés. La référence SKU par défaut sera toujours A2_v2/A2 et gratuite.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Résolution du problème lié à Hive Warehouse Connector
Un problème de convivialité du connecteur Hive Warehouse se posait dans la version précédente. Ce problème est à présent résolu. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Résolution du problème de zéros non significatifs tronqués par le notebook Zeppelin
Zeppelin tronquait de manière incorrecte les zéros non significatifs dans la sortie de tableau pour le format de chaîne. Nou savons résolu ce problème dans cette version.

## <a name="component-version-change"></a>Changement de la version des composants
Aucune modification de la version des composants pour cette version. Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).
