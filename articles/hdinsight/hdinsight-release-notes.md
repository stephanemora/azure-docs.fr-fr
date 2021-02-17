---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f6b7000812f1adfe6ff7bd93711c9b8fe4ff9adc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988362"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

Si vous souhaitez vous abonner aux notes de publication, regardez les communiqués sur [ce référentiel GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-02052021"></a>Date de publication : 05/02/2021

Cette version s’applique à la fois à HDInsight 3.6 et HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="dav4-series-support"></a>Prise en charge de la série Dav4
HDInsight a ajouté la prise en charge de la série Dav4 dans cette version. [En savoir plus sur la série Dav4](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series).

### <a name="kafka-rest-proxy-ga"></a>Disponibilité générale du proxy REST Kafka 
Le proxy REST Kafka vous permet d’interagir avec votre cluster Kafka via une API REST sur HTTPS. Le proxy REST Kafka est en disponibilité générale à partir de cette version. [En savoir plus sur le proxy REST Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. Le service migre progressivement vers les [groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/overview.md). L’ensemble du processus peut prendre plusieurs mois. Une fois les régions et les abonnements migrés, les clusters HDInsight nouvellement créés s’exécuteront sur des groupes de machines virtuelles identiques sans l’intervention du client. Aucun changement cassant n’est prévu.

## <a name="deprecation"></a>Dépréciation
### <a name="disabled-vm-sizes"></a>Tailles de machine virtuelle désactivées
À compter du 9 janvier 2021, HDInsight bloquera tous les clients qui créent des clusters en utilisant les tailles de machine virtuelle standand_A8, standand_A9, standand_A10 et standand_A11. Les clusters existants fonctionneront tels quels. Envisagez de migrer vers HDInsight 4.0 pour éviter une éventuelle interruption du système ou du support.

## <a name="behavior-changes"></a>Changements de comportement
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Remplacement des tailles de machine virtuelle du cluster par défaut par la série Ev3 
Les tailles de machine virtuelle du cluster par défaut seront modifiées de la série D à la série Ev3. Cette modification s’applique aux nœuds principaux et aux nœuds Worker. Pour éviter que cette modification n’ait une incidence sur vos workflows déjà testés, spécifiez les tailles de machine virtuelle que vous souhaitez utiliser dans le modèle ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Ressource d’interface réseau non visible pour les clusters s’exécutant sur des groupes de machines virtuelles identiques Azure
HDInsight migre progressivement vers les groupes de machines virtuelles identiques Azure. Les interfaces réseau des machines virtuelles ne sont plus visibles par les clients pour les clusters qui utilisent des groupes de machines virtuelles identiques Azure.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Changement cassant pour .NET pour Apache Spark 1.0.0
HDInsight introduit la première version majeure officielle de .NET pour Apache Spark dans la prochaine version. Elle apporte l’exhaustivité de l’API DataFrame pour Spark 2.4.x et Spark 3.0.x, ainsi que d’autres fonctionnalités. Cette version majeure introduira des changements cassants. Pour connaître la procédure nécessaire à la mise à jour de votre code et de vos pipelines, consultez [ce guide de migration](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10). En savoir plus [ici](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions.

### <a name="default-cluster-version-will-be-changed-to-40"></a>La version de cluster par défaut sera remplacée par la version 4.0
À partir de février 2021, la version par défaut du cluster HDInsight passera de la version 3.6 à 4.0. Pour plus d’informations sur les versions disponibles, consultez [Versions disponibles](./hdinsight-component-versioning.md#available-versions). [En savoir plus sur les nouveautés de HDInsight 4.0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Mise à niveau de la version du système d’exploitation
HDInsight met à niveau la version du système d’exploitation d’Ubuntu 16.04 à 18.04. La mise à niveau sera terminée avant avril 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Fin de la prise en charge de HDInsight 3.6 le 30 juin 2021
HDInsight 3.6 ne sera plus pris en charge. À partir du 30 juin 2021, les clients ne pourront plus créer de nouveaux clusters HDInsight 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers HDInsight 4.0 pour éviter une éventuelle interruption du système ou du support.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Aucune modification de la version des composants pour cette version. Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](./hdinsight-component-versioning.md).

