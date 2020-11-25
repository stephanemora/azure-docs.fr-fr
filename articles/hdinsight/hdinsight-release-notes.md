---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 1ae4e2a1e0d67a0a09c19b517245ffc6d92d17df
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629918"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

Si vous souhaitez vous abonner aux notes de publication, regardez les communiqués sur [ce référentiel GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11092020"></a>Date de publication : 09/11/2020

Cette version s’applique à la fois à HDInsight 3.6 et HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="hdinsight-identity-broker-hib-is-now-ga"></a>HDInsight Identity Broker (HIB) est désormais en disponibilité générale
HDInsight Identity Broker (HIB) qui active l’authentification OAuth pour les clusters ESP est désormais en mis à la disposition générale avec cette version. Les clusters HIB créés après cette version seront dotés des fonctionnalités HIB les plus récentes :
- Haute disponibilité (HA)
- Prise en charge de Multi-Factor Authentication (MFA)
- Les utilisateurs fédérés se connectent à AAD-DS sans synchronisation de hachage de mot de passe. Pour plus d’informations, consultez la [documentation HIB](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. À compter de cette version, le service migrera progressivement vers des [groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). L’ensemble du processus peut prendre plusieurs mois. Une fois les régions et les abonnements migrés, les clusters HDInsight nouvellement créés s’exécuteront sur des groupes de machines virtuelles identiques sans l’intervention du client. Aucun changement cassant n’est prévu.

## <a name="deprecation"></a>Dépréciation
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Dépréciation du cluster ML Services HDInsight 3.6
Le type de cluster ML Services HDInsight 3.6 ne sera plus pris en charge au 31 décembre 2020. Après cette date, les clients ne devront plus créer de cluster ML Services 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Vérifiez l’expiration de la prise en charge des versions HDInsight et des types de cluster [ici](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

### <a name="disabled-vm-sizes"></a>Tailles de machine virtuelle désactivées
À compter du 16 novembre 2020, HDInsight bloquera les nouveaux clients qui créent des clusters en utilisant les tailles de machine virtuelle standand_A8, standand_A9, standand_A10 et standand_A11. Les clients existants qui ont utilisé ces tailles de machine virtuelle au cours des trois derniers mois ne seront pas concernés. À compter du 9 janvier 2021, HDInsight bloquera tous les clients qui créent des clusters en utilisant les tailles de machine virtuelle standand_A8, standand_A9, standand_A10 et standand_A11. Les clusters existants fonctionneront tels quels. Envisagez de migrer vers HDInsight 4.0 pour éviter une éventuelle interruption du système ou du support.

## <a name="behavior-changes"></a>Changements de comportement
Cette version n’est associée à aucun changement de comportement.

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Possibilité de sélectionner différentes tailles de machine virtuelle Zookeeper pour les services Spark, Hadoop et ML
HDInsight ne prend actuellement pas en charge la personnalisation de la taille de nœud Zookeeper pour les types de cluster des services Spark, Hadoop et ML. Les tailles de machine virtuelle sont définies par défaut sur A2_v2/A2, qui sont fournies sans frais. Dans la version à venir, vous pourrez sélectionner la taille de machine virtuelle Zookeeper la plus appropriée pour votre scénario. Les nœuds Zookeeper avec une taille de machine virtuelle différente de A2_v2/A2 sont facturés. Les machines virtuelles A2_v2 et A2 sont toujours fournies sans frais.

### <a name="default-cluster-version-will-be-changed-to-40"></a>La version de cluster par défaut sera remplacée par la version 4.0
À partir de février 2021, la version par défaut du cluster HDInsight passera de la version 3.6 à 4.0. Pour plus d’informations sur les versions disponibles, consultez [Versions disponibles](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions). En savoir plus sur les nouveautés de [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release).

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Fin de la prise en charge de HDInsight 3.6 le 30 juin 2021
HDInsight 3.6 ne sera plus pris en charge. À partir du 30 juin 2021, les clients ne pourront plus créer de nouveaux clusters HDInsight 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers HDInsight 4.0 pour éviter une éventuelle interruption du système ou du support.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 
### <a name="fix-issue-for-restarting-vms-in-cluster"></a>Correction du problème de redémarrage des machines virtuelles dans le cluster
Le problème de redémarrage des machines virtuelles dans le cluster a été corrigé, vous pouvez de nouveau utiliser [PowerShell ou l’API REST pour redémarrer les nœuds dans le cluster](https://docs.microsoft.com/azure/hdinsight/cluster-reboot-vm).

## <a name="component-version-change"></a>Changement de la version des composants
Aucune modification de la version des composants pour cette version. Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](./hdinsight-component-versioning.md).
