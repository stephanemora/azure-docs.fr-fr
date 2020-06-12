---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: d0f12274f3cbc0cd2726097e0507079cb858d59f
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116568"
---
# <a name="release-notes"></a>Notes de publication

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

## <a name="release-date-01092020"></a>Date de publication : 09/01/2020

Cette version s’applique à la fois à HDInsight 3.6 et 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les modifications ci-dessous, veuillez attendre quelques jours pour que la mise en production soit active dans votre région.

> [!IMPORTANT]  
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [l’article sur le contrôle de version de HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="tls-12-enforcement"></a>Application de TLS 1.2
TLS (Transport Layer Security) et SSL (Secure Sockets Layer) sont des protocoles de chiffrement qui permettent la sécurité des communications sur un réseau d’ordinateurs. Apprenez-en davantage sur [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight utilise TLS 1.2 sur les points de terminaison HTTPS publics, mais TLS 1.1 est toujours pris en charge à des fins de compatibilité descendante. 

Avec cette version, les clients peuvent choisir TLS 1.2 uniquement pour toutes les connexions qui passent par le point de terminaison de cluster public. Pour prendre cela en charge, vous pouvez spécifier la nouvelle propriété **minSupportedTlsVersion** lors de la création du cluster. Si cette propriété n’est pas définie, le cluster prend toujours en charge TLS 1.0, 1.1 et 1.2, ce qui est identique au comportement actuel. Les clients peuvent affecter la valeur « 1.2 » à cette propriété, ce qui signifie que le cluster ne prend en charge que TLS 1.2 et versions ultérieures. Pour plus d’informations, consultez [TLS](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>BYOK (Bring Your Own Key) pour le chiffrement de disque
Tous les disques managés dans HDInsight sont protégés par Azure Storage Service Encryption (SSE). Par défaut, les données stockées sur ces disques sont chiffrées à l’aide de clés managées par Microsoft. À compter de cette version, vous pouvez utiliser votre propre clé (BYOK, Bring Your Own Key) pour le chiffrement de disque et effectuer la gestion à l’aide d’Azure Key Vault. Le chiffrement BYOK est une configuration en une étape qui a lieu lors de la création d’un cluster, sans coût supplémentaire. Il vous suffit d’inscrire HDInsight comme identité managée auprès d’Azure Key Vault et d’ajouter la clé de chiffrement pendant la création du cluster. Pour plus d’informations, consultez [Chiffrement de disque avec clé gérée par le client](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Dépréciation
Cette version ne fait pas l’objet d’une dépréciation. Pour vous préparer aux dépréciations à venir, consultez [Modifications à venir](#upcoming-changes).

## <a name="behavior-changes"></a>Changements de comportement
Cette version n’est associée à aucun changement de comportement. Pour vous préparer aux changements à venir, consultez [Changements à venir](#upcoming-changes).

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions. 

### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Désapprobation de Spark 2.1 et 2.2 dans le cluster Spark HDInsight 3.6
À compter du 1er juillet 2020, les clients ne pourront pas créer de nouveaux clusters Spark avec les versions 2.1 et 2.2 de Spark sur HDInsight 3.6. Les clusters existants s’exécuteront en l’état sans support de Microsoft. Effectuez la migration vers Spark 2.3 sur HDInsight 3.6 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support. Pour plus d’infrmations, consultez [Migrer des charges de travail Apache Spark 2.1 et 2.2 vers les versions 2.3 et 2.4](./spark/migrate-versions.md).

### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Désapprobation de Spark 2.3 dans le cluster Spark HDInsight 4.0
À compter du 1er juillet 2020, les clients ne pourront pas créer de nouveaux clusters Spark avec la version 2.3 de Spark sur HDInsight 4.0. Les clusters existants s’exécuteront en l’état sans support de Microsoft. Effectuez la migration vers Spark 2.4 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support. Pour plus d’infrmations, consultez [Migrer des charges de travail Apache Spark 2.1 et 2.2 vers les versions 2.3 et 2.4](./spark/migrate-versions.md).

### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Désapprobation de Kafka 1.1 dans le cluster Kafka HDInsight 4.0
À compter du 1er juillet 2020, les clients ne pourront pas créer de nouveaux clusters Kafka avec Kafka 1.1 sur HDInsight 4.0. Les clusters existants s’exécuteront en l’état sans support de Microsoft. Envisagez de migrer vers Kafka 2.1 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support. Pour plus d’informations, consultez [Migrer des charges de travail Apache Kafka vers Azure HDInsight 4.0](./kafka/migrate-versions.md).

### <a name="hbase-20-to-216"></a>HBase 2.0 vers 2.1.6
Dans la prochaine version de HDInsight 4.0, la version de HBase sera mise à niveau de la version 2.0 vers 2.1.6

### <a name="spark-240-to-244"></a>Spark 2.4.0 vers 2.4.4
Dans la prochaine version de HDInsight 4.0, la version de Spark sera mise à niveau de la version 2.4.0 vers 2.4.4.

### <a name="kafka-210-to-211"></a>Kafka 2.1.0 vers 2.1.1
Dans la prochaine version de HDInsight 4.0, la version de Kafka sera mise à niveau de la version 2.1.0 vers 2.1.1.

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Une machine virtuelle à au moins quatre cœurs est nécessaire pour le nœud principal 
Une machine virtuelle à au moins quatre cœurs est nécessaire pour le nœud principal, afin de garantir la haute disponibilité et la fiabilité des clusters HDInsight. À compter du 6 avril 2020, les clients peuvent uniquement choisir des machines virtuelles à quatre cœurs ou plus comme nœud principal pour les nouveaux clusters HDInsight. Les clusters existants continueront à s’exécuter comme prévu. 

### <a name="esp-spark-cluster-node-size-change"></a>Modification de la taille du nœud de cluster ESP Spark 
Dans la version à venir, la taille de nœud minimale autorisée pour le cluster ESP Spark sera remplacée par la taille Standard_D13_V2. Les machines virtuelles de la série A peuvent provoquer des problèmes liés au cluster ESP, en raison d’une capacité de mémoire et de processeur relativement faible. L’utilisation des machines virtuelles de la série A sera dépréciée pour la création de nouveaux clusters ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. Dans la version à venir, HDInsight utilisera à la place des groupes de machines virtuelles identiques Azure. Apprenez-en davantage sur les groupes de machines virtuelles identiques Azure.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Aucune modification de la version des composants pour cette version. Vous pouvez trouver les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 ici.

