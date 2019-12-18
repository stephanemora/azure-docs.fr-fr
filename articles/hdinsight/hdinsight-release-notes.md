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
ms.openlocfilehash: 5d9ca8d0df3eb0186add5c40765c87a4409a5660
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926425"
---
# <a name="release-notes"></a>Notes de publication

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l'un des services les plus populaires parmi les clients d'entreprise pour l'analytique Apache Hadoop et Apache Spark open source sur Azure.

## <a name="release-date-11072019"></a>Date de publication : 07/11/2019

Cette version s’applique à la fois à HDInsight 3.6 et 4.0.

> [!IMPORTANT]  
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [l’article sur le contrôle de version de HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nouvelles fonctionnalités

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight Identity Broker (HIB) (préversion)

HDInsight Identity Broker (HIB) permet aux utilisateurs de se connecter à Apache Ambari à l’aide de l’authentification multifacteur (MFA) et d’accéder aux tickets Kerberos requis sans avoir besoin de hachages de mot de passe dans Azure Active Directory Domain Services (AAD-DS). Actuellement, HIB est disponible uniquement pour les clusters déployés via un modèle Resource Manager.

### <a name="kafka-rest-api-proxy-preview"></a>Proxy d’API REST Kafka (préversion)

Le proxy d’API REST Kafka fournit un déploiement en un clic d’un proxy REST hautement disponible avec un cluster Kafka via une autorisation AAD sécurisée et un protocole OAuth. 

### <a name="auto-scale"></a>Mise à l’échelle automatique

La mise à l’échelle automatique pour Azure HDInsight est désormais généralement disponible dans toutes les régions pour les types de cluster Apache Spark et Hadoop. Cette fonctionnalité permet de gérer les charges de travail d’analytique de Big Data Analytics de manière plus rentable et productive. Vous pouvez désormais optimiser l’utilisation de vos clusters HDInsight et payer uniquement ce dont vous avez besoin.

Selon vos besoins, vous pouvez choisir entre une mise à l’échelle automatique basée sur la charge et une mise à l’échelle automatique basée sur la planification. La mise à l’échelle automatique basée sur la charge permet d’augmenter ou de réduire la taille du cluster en fonction des besoins actuels en ressources, tandis que la mise à l’échelle automatique basée sur la planification permet de modifier la taille du cluster en fonction d’une planification prédéfinie. 

La prise en charge de la mise à l’échelle automatique pour charge de travail HBase et LLAP est également disponible en préversion publique. Pour plus d’informations, consultez [Mettre à l’échelle automatiquement les clusters Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters).

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Écritures accélérées pour Apache HBase dans HDInsight 

La fonctionnalité Écritures accélérées utilise des disques managés SSD Premium Azure pour améliorer les performances du journal WAL (write-ahead log) Apache HBase. Pour plus d’informations, consultez [Écritures accélérées pour Apache HBase dans Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes).

### <a name="custom-ambari-db"></a>Base de données Ambari personnalisée

HDInsight offre désormais une nouvelle capacité pour permettre aux clients d’utiliser leur propre base de données SQL pour Ambari. À présent, les clients peuvent choisir la base de données SQL appropriée pour Ambari et facilement la mettre à niveau en fonction de leurs propres besoins en croissance commerciale. Le déploiement est effectué avec un modèle Azure Resource Manager. Pour plus d’informations, consultez [Configurer des clusters HDInsight avec une base de données Ambari personnalisée](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db).

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>Les machines virtuelles de la série F sont désormais disponibles avec HDInsight

Les machines virtuelles de la série F peuvent être un bon choix pour prendre en main HDInsight avec des exigences de traitement légères. Affichant le coût le plus bas par heure, la série F offre le meilleur rapport prix-performances de la gamme Azure si l’on considère les unités de calcul Azure (ACU) par processeur virtuel. Pour plus d’informations, consultez [Sélection de la taille de machine virtuelle adaptée à votre cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size).

## <a name="deprecation"></a>Dépréciation

### <a name="g-series-virtual-machine-deprecation"></a>Dépréciation des machines virtuelles de la série G
À partir de cette version, les machines virtuelles de la série G ne sont plus proposées dans HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>Dépréciation des machines virtuelles Dv1
À partir de cette version, l’utilisation de machines virtuelles Dv1 avec HDInsight est déconseillée. Toute demande client concernant Dv1 sera traitée automatiquement avec Dv2. Il n’existe pas de différence de prix entre les machines virtuelles Dv1 et Dv2.

## <a name="behavior-changes"></a>Changements de comportement

### <a name="cluster-managed-disk-size-change"></a>Modification de la taille du disque managé par le cluster
HDInsight fournit un espace disque managé avec le cluster. À partir de cette version, la taille du disque managé de chaque nœud dans le nouveau cluster créé est modifiée à 128 Go.

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. À partir de décembre, HDInsight utilisera à la place les groupes de machines virtuelles identiques Azure. En savoir plus sur les [groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>HBase 2.0 à 2.1
Dans la prochaine version de HDInsight 4.0, la version de HBase sera mise à niveau de la version 2.0 à 2.1.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Dépréciation des machines virtuelles de la série A pour le cluster ESP
Les machines virtuelles de la série A peuvent provoquer des problèmes liés au cluster ESP en raison d’une capacité de mémoire et de l’UC relativement faible. Dans la prochaine version, l’utilisation des machines virtuelles de la série A sera déconseillée pour la création de nouveaux clusters ESP.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Il n’y a aucune modification de la version des composants pour cette version. Vous pouvez trouver les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 [ici](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
