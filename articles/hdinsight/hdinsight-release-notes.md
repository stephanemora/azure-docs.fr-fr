---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065495"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

Si vous souhaitez vous abonner aux notes de publication, regardez les communiqués sur [ce référentiel GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Date de publication : 24/03/2021

Cette version s’applique à la fois à HDInsight 3.6 et HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="spark-30-preview"></a>Version préliminaire Spark 3.0
HDInsight a ajouté la prise en charge de [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) à HDInsight 4.0 en tant que fonctionnalité d’évaluation. 

### <a name="kafka-24-preview"></a>Version préliminaire Kafka 2.4
HDInsight a ajouté la prise en charge de [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) à HDInsight 4.0 en tant que fonctionnalité d’évaluation.

### <a name="eav4-series-support"></a>Prise en charge de la série Eav4
HDInsight a ajouté la prise en charge de la série Eav4 dans cette version. [En savoir plus sur la série Dav4](../virtual-machines/eav4-easv4-series.md). La série a été mise à disposition dans les régions suivantes : 

* Australie Est
* Brésil Sud
* USA Centre
* Asie Est
* USA Est
* Japon Est
* Asie Sud-Est
* Sud du Royaume-Uni
* Europe Ouest
* USA Ouest 2

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. Le service migre progressivement vers les [groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/overview.md). L’ensemble du processus peut prendre plusieurs mois. Une fois les régions et les abonnements migrés, les clusters HDInsight nouvellement créés s’exécuteront sur des groupes de machines virtuelles identiques sans l’intervention du client. Aucun changement cassant n’est prévu.

## <a name="deprecation"></a>Dépréciation
Cette version ne fait l’objet d’aucune dépréciation.

## <a name="behavior-changes"></a>Changements de comportement
### <a name="default-cluster-version-is-changed-to-40"></a>La version de cluster par défaut est remplacée par la version 4.0
La version par défaut du cluster HDInsight passe de la version 3.6 à la version 4.0. Pour plus d’informations sur les versions disponibles, consultez [Versions disponibles](./hdinsight-component-versioning.md). [En savoir plus sur les nouveautés de HDInsight 4.0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Les tailles de machine virtuelle du cluster par défaut sont remplacées par la série Ev3 
Les tailles de machine virtuelle du cluster par défaut passent de la série D à la série Ev3. Cette modification s’applique aux nœuds principaux et aux nœuds Worker. Pour éviter que cette modification n’ait une incidence sur vos workflows déjà testés, spécifiez les tailles de machine virtuelle que vous souhaitez utiliser dans le modèle ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Ressource d’interface réseau non visible pour les clusters s’exécutant sur des groupes de machines virtuelles identiques Azure
HDInsight migre progressivement vers les groupes de machines virtuelles identiques Azure. Les interfaces réseau des machines virtuelles ne sont plus visibles par les clients pour les clusters qui utilisent des groupes de machines virtuelles identiques Azure.

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions.

### <a name="os-version-upgrade"></a>Mise à niveau de la version du système d’exploitation
HDInsight mettra à niveau la version du système d’exploitation d’Ubuntu 16.04 à 18.04. La mise à niveau sera terminée avant avril 2021.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Support de base pour HDInsight 3.6 à partir du 1er juillet 2021
À partir du 1er juillet 2021, Microsoft proposera un [Support de base](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) pour certains types de cluster HDInsight 3.6. Le plan de Support de base sera disponible jusqu’au 3 avril 2022. Vous serez automatiquement inscrit au Support de base à partir du 1er juillet 2021. Aucune action n’est requise pour vous inscrire. Consultez [notre documentation](hdinsight-36-component-versioning.md) sur les types de clusters inclus dans le Support de base. 

Nous vous déconseillons de créer de nouvelles solutions sur HDInsight 3.6, figez les modifications sur les environnements 3.6 existants. Nous vous recommandons de [migrer vos clusters vers HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). En savoir plus sur [les nouveautés de HDInsight 4.0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Ajout de la prise en charge de Spark 3.0.0 et Kafka 2.4.1 comme version préliminaire. Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](./hdinsight-component-versioning.md).

## <a name="recommanded-features"></a>Fonctionnalités recommandées
### <a name="service-tags"></a>Balises de service
Les balises de service simplifient la restriction de l’accès réseau aux services Azure pour les machines virtuelles Azure et les réseaux virtuels Azure. Les balises de service dans vos règles de groupe de sécurité réseau (NSG) autorisent ou refusent le trafic vers un service Azure spécifique. La règle peut être définie globalement ou par région Azure. Azure fournit la maintenance des adresses IP sous-tendant chaque balise. Les balises de service HDInsight pour les groupes de sécurité réseau sont des groupes d’adresses IP pour les services d’intégrité et de gestion. Ces groupes permettent de réduire la complexité de la création de règles de sécurité. Les clients HDInsight peuvent activer l’étiquette de service via le portail Azure, PowerShell et l’API REST. Pour plus d’informations, consultez [Étiquettes de service de groupe de sécurité réseau (NSG) pour Azure HDInsight](./hdinsight-service-tags.md).
