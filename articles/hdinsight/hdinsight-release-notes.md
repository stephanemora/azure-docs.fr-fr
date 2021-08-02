---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/02/2021
ms.openlocfilehash: 61b32852de0cb15db3860b72561f87e56db8f40d
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111903848"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

Si vous souhaitez vous abonner aux notes de publication, regardez les communiqués sur [ce référentiel GitHub](https://github.com/hdinsight/release-notes/releases).


## <a name="release-date-06022021"></a>Date de publication : 02/06/2021

Cette version s’applique à la fois à HDInsight 3.6 et HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

Les versions de système d’exploitation pour cette mise en production sont les suivantes :
- HDInsight 3.6 : Ubuntu 16.04.7 LTS
- HDInsight 4.0 : Ubuntu 18.04.5 LTS

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="os-version-upgrade"></a>Mise à niveau de la version du système d’exploitation
Comme indiqué dans l’article sur le [cycle de publication d’Ubuntu](https://ubuntu.com/about/release-cycle), le noyau Ubuntu 16.04 arrivera en fin de vie (EOL) en avril 2021. Nous avons commencé à déployer la nouvelle image de cluster HDInsight 4.0 qui s’exécute sur Ubuntu 18.04 avec cette mise en production. Une fois disponibles, les clusters HDInsight 4.0 nouvellement créés s’exécuteront par défaut sur Ubuntu 18.04. Les clusters existants sur Ubuntu 16.04 s’exécuteront en l’état avec une prise en charge complète.

HDInsight 3.6 continuera de s’exécuter sur Ubuntu 16.04. Il passera au support De base (au lieu du support Standard) à partir du 1er juillet 2021. Pour plus d’informations sur les dates et les options de prise en charge, consultez [Versions d’Azure HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions). Ubuntu 18.04 ne sera pas pris en charge pour HDInsight 3.6. Si vous souhaitez utiliser Ubuntu 18.04, vous devez migrer vos clusters vers HDInsight 4.0. 

Vous devez supprimer et recréer vos clusters si vous souhaitez déplacer des clusters HDInsight 4.0 existants vers Ubuntu 18.04. Prévoyez de créer ou de recréer vos clusters une fois que la prise en charge d’Ubuntu 18.04 devient disponible.

Après avoir créé le nouveau cluster, vous pouvez établir une connexion SSH à votre cluster et exécuter `sudo lsb_release -a` pour vérifier qu’il s’exécute sur Ubuntu 18.04. Nous vous recommandons de tester d’abord vos applications dans vos abonnements de test avant de passer à la production. [En savoir plus sur la mise à jour de HDInsight Ubuntu 18.04](./hdinsight-ubuntu-1804-qa.md).

### <a name="scaling-optimizations-on-hbase-accelerated-writes-clusters"></a>Optimisations de la mise à l’échelle sur les clusters d’écritures accélérées HBase
HDInsight a apporté quelques améliorations et optimisations concernant la mise à l’échelle pour les clusters activés en écriture accélérée HBase. [En savoir plus sur l’écriture accélérée HBase](./hbase/apache-hbase-accelerated-writes.md).

## <a name="deprecation"></a>Dépréciation
Cette version ne fait l’objet d’aucune dépréciation.

## <a name="behavior-changes"></a>Changements de comportement
### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsight-40"></a>Désactiver la taille de machine virtuelle Stardard_A5 comme nœud principal pour HDInsight 4.0
Le nœud principal de cluster HDInsight est chargé de l’initialisation et de la gestion du cluster. La taille de machine virtuelle Standard_A5 présente des problèmes de fiabilité en tant que nœud principal pour HDInsight 4.0. À partir de cette version, les clients ne pourront plus créer de nouveaux clusters avec la taille de machine virtuelle Standard_A5 comme nœud principal. Vous pouvez utiliser d’autres machines virtuelles à deux cœurs comme E2_v3 ou E2s_v3. Les clusters existants fonctionneront tels quels. Une machine virtuelle à quatre cœurs est fortement recommandée pour le nœud principal afin de garantir la haute disponibilité et la fiabilité des clusters HDInsight de production.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Ressource d’interface réseau non visible pour les clusters s’exécutant sur des groupes de machines virtuelles identiques Azure
HDInsight migre progressivement vers les groupes de machines virtuelles identiques Azure. Les interfaces réseau des machines virtuelles ne sont plus visibles par les clients pour les clusters qui utilisent des groupes de machines virtuelles identiques Azure.

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions.

### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>La requête interactive HDInsight prend uniquement en charge la mise à l’échelle automatique basée sur une planification

À mesure que les scénarios client augmentent et se diversifient, nous avons identifié certaines limitations avec la mise à l’échelle automatique basée sur la charge LLAP (Interactive Query). Ces limitations sont dues à la nature de la dynamique des requêtes LLAP, aux problèmes de précision de la prédiction de la charge future et aux problèmes dans la redistribution des tâches du planificateur LLAP. En raison de ces limitations, les utilisateurs peuvent voir leurs requêtes s’exécuter plus lentement sur les clusters LLAP lorsque la mise à l’échelle automatique est activée. L’impact sur les performances peut être plus important que le coût de la mise à l’échelle automatique.

À partir de juillet 2021, la charge de travail Interactive Query dans HDInsight prend uniquement en charge la mise à l’échelle automatique basée sur la planification. Vous ne pouvez plus activer la mise à l’échelle automatique sur les nouveaux clusters Interactive Query. Les clusters en cours d’exécution existants peuvent continuer à s’exécuter avec les limitations connues décrites ci-dessus. 

Microsoft vous recommande de passer à une mise à l’échelle automatique basée sur une planification pour LLAP.  Vous pouvez analyser le modèle d’utilisation actuel de votre cluster via le tableau de bord Grafana Hive. Pour plus d’informations, consultez [Mettre à l’échelle automatiquement les clusters Azure HDInsight](hdinsight-autoscale-clusters.md). 

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Support de base pour HDInsight 3.6 à partir du 1er juillet 2021
À partir du 1er juillet 2021, Microsoft proposera un [Support de base](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) pour certains types de cluster HDInsight 3.6. Le plan de Support de base sera disponible jusqu’au 3 avril 2022. Vous serez automatiquement inscrit au Support de base à partir du 1er juillet 2021. Aucune action n’est requise pour vous inscrire. Consultez [notre documentation](hdinsight-36-component-versioning.md) sur les types de clusters inclus dans le Support de base. 

Nous vous déconseillons de créer de nouvelles solutions sur HDInsight 3.6, figez les modifications sur les environnements 3.6 existants. Nous vous recommandons de [migrer vos clusters vers HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). En savoir plus sur [les nouveautés de HDInsight 4.0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

### <a name="vm-host-naming-will-be-changed-on-july-1-2021"></a>La convention d’affectation de noms pour les hôtes de machine virtuelle sera modifié le 1er juillet 2021
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. Le service migre progressivement vers les [groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/overview.md). Cette migration modifiera le format du nom de domaine complet des noms d’hôte de cluster, et la séquence des nombres dans le nom d’hôte ne sera pas garantie. Si vous souhaitez obtenir les noms de domaine complets pour chaque nœud, référez-vous à [Rechercher les noms d’hôte des nœuds de cluster](./find-host-name.md).

### <a name="move-to-azure-virtual-machine-scale-sets"></a>Passer à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. Le service migre progressivement vers les [groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/overview.md). L’ensemble du processus peut prendre plusieurs mois. Une fois les régions et les abonnements migrés, les clusters HDInsight nouvellement créés s’exécuteront sur des groupes de machines virtuelles identiques sans l’intervention du client. Aucun changement cassant n’est prévu.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](./hdinsight-component-versioning.md).
