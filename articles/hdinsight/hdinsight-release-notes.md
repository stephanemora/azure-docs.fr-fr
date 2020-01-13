---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435423"
---
# <a name="release-notes"></a>Notes de publication

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l'un des services les plus populaires parmi les clients d'entreprise pour l'analytique Apache Hadoop et Apache Spark open source sur Azure.

## <a name="release-date-12172019"></a>Date de publication : 17/12/2019

Cette version s’applique à la fois à HDInsight 3.6 et 4.0.

> [!IMPORTANT]  
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [l’article sur le contrôle de version de HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nouvelles fonctionnalités

### <a name="service-tags"></a>Balises de service
Les balises de service simplifient la sécurité des machines virtuelles Azure et des réseaux virtuels Azure en vous permettant de limiter facilement l’accès réseau aux services Azure. Vous pouvez utiliser des balises de service dans vos règles de groupe de sécurité réseau pour autoriser ou refuser le trafic vers un service Azure spécifique à l’échelle mondiale ou par région Azure. Azure fournit la maintenance des adresses IP sous-tendant chaque balise. Les balises de service HDInsight pour les groupes de sécurité réseau sont des groupes d’adresses IP pour les services d’intégrité et de gestion. Ces groupes permettent de réduire la complexité de la création de règles de sécurité. Les clients HDInsight peuvent activer la balise de service via le portail Azure, PowerShell et l’API REST. Pour plus d’informations, consultez [Étiquettes de service de groupe de sécurité réseau (NSG) pour Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags).

### <a name="custom-ambari-db"></a>Base de données Ambari personnalisée
HDInsight vous permet à présent d’utiliser votre propre base de données SQL pour Apache Ambari. Vous pouvez configurer cette instance Ambari DB personnalisée à partir du portail Azure ou par le biais du modèle Resource Manager.  Cette fonctionnalité vous permet de choisir la base de données SQL adaptée à vos besoins en matière de traitement et de capacité. Vous pouvez également effectuer une mise à niveau facilement pour répondre aux besoins de croissance de l’entreprise. Pour plus d’informations, consultez [Configurer des clusters HDInsight avec une base de données Ambari personnalisée](hdinsight-custom-ambari-db.md).

![Base de données Ambari personnalisée](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Dépréciation
Cette version ne fait pas l’objet d’une dépréciation. Pour vous préparer aux dépréciations à venir, consultez [Modifications à venir](#upcoming-changes).

## <a name="behavior-changes"></a>Changements de comportement
Cette version n’est associée à aucun changement de comportement. Pour vous préparer aux changements de comportement à venir, consultez [Modifications à venir](#upcoming-changes).

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Application de TLS (Transport Layer Security) 1.2
TLS (Transport Layer Security) et SSL (Secure Sockets Layer) sont des protocoles de chiffrement qui permettent la sécurité des communications sur un réseau d’ordinateurs. Pour plus d’informations, consultez [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Alors que les clusters Azure HDInsight acceptent les connexions TLS 1.2 sur les points de terminaison HTTPS publics, TLS 1.1 est toujours pris en charge pour la compatibilité descendante avec les clients plus anciens.

À partir de la prochaine version, vous serez en mesure d’activer et de configurer vos nouveaux clusters HDInsight pour qu’ils acceptent uniquement les connexions TLS 1.2. 

Plus tard dans l’année, à compter du 30/06/2020, Azure HDInsight appliquera TLS 1.2 ou versions ultérieures pour toutes les connexions HTTPS. Nous vous recommandons de vous assurer que tous vos clients sont prêts à gérer TLS 1.2 ou versions ultérieures.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. À partir de février 2020 (la date sera précisée ultérieurement), HDInsight utilisera à la place les groupes de machines virtuelles identiques Azure. En savoir plus sur les [groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>Modification de la taille du nœud de cluster ESP Spark 
Dans la prochaine version :
- La taille de nœud minimale autorisée pour le cluster ESP Spark sera remplacée par la taille Standard_D13_V2. 
- Les machines virtuelles de série A seront dépréciées pour la création de nouveaux clusters ESP, car elles peuvent provoquer des problèmes de cluster ESP en raison d’une capacité de processeur et de mémoire relativement faible.

### <a name="hbase-20-to-21"></a>HBase 2.0 à 2.1
Dans la prochaine version de HDInsight 4.0, la version de HBase sera mise à niveau de la version 2.0 à 2.1.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Nous avons prolongé la prise en charge de HDInsight 3.6 jusqu’au 31 décembre 2020. Pour plus d’informations, consultez la page [Versions HDInsight prises en charge](hdinsight-component-versioning.md#supported-hdinsight-versions).

Il n’y a aucune modification de la version des composants pour HDInsight 4.0.

Apache Zeppelin sur HDInsight 3.6 : 0.7.0-->0.7.3. 

Vous pouvez trouver les versions les plus récentes des composants dans [ce document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="new-regions"></a>Nouvelles régions

### <a name="uae-north"></a>Émirats arabes unis Nord
Les adresses IP de gestion de la région Émirats arabes unis Nord sont les suivantes : `65.52.252.96` et `65.52.252.97`.
