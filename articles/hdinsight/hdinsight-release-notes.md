---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 0c14e5537385b7518fd08d9d3599993bc6d82f88
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535499"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

## <a name="release-date-10082020"></a>Date de publication : 08/10/2020

Cette version s’applique à la fois à HDInsight 3.6 et HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Clusters privés HDInsight sans adresse IP publique ni Private Link (aperçu)
HDInsight prend désormais en charge la création de clusters sans adresse IP publique ni accès Private Link aux clusters en préversion. Les clients peuvent utiliser les nouveaux paramètres avancés de mise en réseau pour créer un cluster entièrement isolé sans adresse IP publique et utiliser leurs propres points de terminaison privés pour accéder au cluster. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. À partir de cette version, le service migrera progressivement vers des [groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/overview.md). L’ensemble du processus peut prendre plusieurs mois. Une fois vos régions et vos abonnements migrés, les clusters HDInsight nouvellement créés seront exécutés sur des groupes identiques de machines virtuelles sans action du client. Aucun changement cassant n’est prévu.

## <a name="deprecation"></a>Dépréciation
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Dépréciation du cluster ML Services HDInsight 3.6
Le type de cluster ML Services HDInsight 3.6 ne sera plus pris en charge au 31 décembre 2020. Après cette date, les clients ne devront plus créer de cluster ML Services 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Vérifiez l’expiration de la prise en charge des versions HDInsight et des types de cluster [ici](./hdinsight-component-versioning.md#available-versions).

## <a name="behavior-changes"></a>Changements de comportement
Cette version n’est associée à aucun changement de comportement.

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Possibilité de sélectionner différentes tailles de machine virtuelle Zookeeper pour les services Spark, Hadoop et ML
HDInsight ne prend actuellement pas en charge la personnalisation de la taille de nœud Zookeeper pour les types de cluster des services Spark, Hadoop et ML. Les tailles de machine virtuelle sont définies par défaut sur A2_v2/A2, qui sont fournies sans frais. Dans la version à venir, vous pourrez sélectionner la taille de machine virtuelle Zookeeper la plus appropriée pour votre scénario. Les nœuds Zookeeper avec une taille de machine virtuelle différente de A2_v2/A2 sont facturés. Les machines virtuelles A2_v2 et A2 sont toujours fournies sans frais.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Aucune modification de la version des composants pour cette version. Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](./hdinsight-component-versioning.md).
