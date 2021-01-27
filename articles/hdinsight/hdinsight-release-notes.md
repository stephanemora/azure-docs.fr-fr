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
ms.openlocfilehash: 5c414a11085a6a37dee6be522dcf513e8990e5e2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786349"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

Si vous souhaitez vous abonner aux notes de publication, regardez les communiqués sur [ce référentiel GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Date de publication : 18/11/2020

Cette version s’applique à la fois à HDInsight 3.6 et HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Rotation automatique des clés pour le chiffrement à clé géré par le client au repos
À partir de cette version, les clients peuvent utiliser des URL de clé de chiffrement sans version Azure KeyValut pour le chiffrement à clé géré par le client au repos. HDInsight fait automatiquement pivoter les clés à mesure qu’elles expirent ou sont remplacées par les nouvelles versions. Vous trouverez plus de détails [ici](./disk-encryption.md).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Possibilité de sélectionner différentes tailles de machine virtuelle Zookeeper pour les services Spark, Hadoop et ML
HDInsight ne prenait auparavant pas en charge la personnalisation de la taille de nœud Zookeeper pour les types de cluster des services Spark, Hadoop et ML. Les tailles de machine virtuelle sont définies par défaut sur A2_v2/A2, qui sont fournies sans frais. À partir de cette version, vous pourrez sélectionner la taille de machine virtuelle Zookeeper la plus appropriée pour votre scénario. Les nœuds Zookeeper avec une taille de machine virtuelle différente de A2_v2/A2 sont facturés. Les machines virtuelles A2_v2 et A2 sont toujours fournies sans frais.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise désormais les machines virtuelles Azure pour approvisionner le cluster. À compter de cette version, le service migrera progressivement vers des [groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/overview.md). L’ensemble du processus peut prendre plusieurs mois. Une fois les régions et les abonnements migrés, les clusters HDInsight nouvellement créés s’exécuteront sur des groupes de machines virtuelles identiques sans l’intervention du client. Aucun changement cassant n’est prévu.

## <a name="deprecation"></a>Dépréciation
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Dépréciation du cluster ML Services HDInsight 3.6
Le type de cluster ML Services HDInsight 3.6 ne sera plus pris en charge au 31 décembre 2020. Après cette date, les clients ne pourront plus créer de cluster ML Services 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Vérifiez l’expiration de la prise en charge des versions HDInsight et des types de cluster [ici](./hdinsight-component-versioning.md#available-versions).

### <a name="disabled-vm-sizes"></a>Tailles de machine virtuelle désactivées
À compter du 16 novembre 2020, HDInsight bloquera les nouveaux clients qui créent des clusters en utilisant les tailles de machine virtuelle standand_A8, standand_A9, standand_A10 et standand_A11. Les clients existants qui ont utilisé ces tailles de machine virtuelle au cours des trois derniers mois ne seront pas concernés. À compter du 9 janvier 2021, HDInsight bloquera tous les clients qui créent des clusters en utilisant les tailles de machine virtuelle standand_A8, standand_A9, standand_A10 et standand_A11. Les clusters existants fonctionneront tels quels. Envisagez de migrer vers HDInsight 4.0 pour éviter une éventuelle interruption du système ou du support.

## <a name="behavior-changes"></a>Changements de comportement
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>Ajout la vérification de la règle NSG avant l'opération de mise à l'échelle
HDInsight a ajouté la vérification des groupes de sécurité réseau (NSG) et des itinéraires définis par l'utilisateur (UDR) à l'opération de mise à l'échelle. La même validation intervient pour la mise à l'échelle des clusters en plus de leur création. Cette validation permet d'éviter les erreurs imprévisibles. Si la validation échoue, la mise à l'échelle échoue également. Pour en savoir plus sur la configuration des NSG et des UDR, reportez-vous à la section [Adresses IP de gestion HDInsight](./hdinsight-management-ip-addresses.md).

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions.

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>La taille par défaut des machines virtuelles de cluster sera remplacée par la famille Ev3
À partir de la prochaine version (vers la fin janvier), les tailles par défaut des machines virtuelles de cluster, c’est-à-dire la famille D, seront remplacées par la famille Ev3. Cette modification s’applique aux nœuds principaux et aux nœuds Worker. Pour éviter cette modification, spécifiez les tailles de machine virtuelle que vous souhaitez utiliser dans le modèle ARM.

### <a name="default-cluster-version-will-be-changed-to-40"></a>La version de cluster par défaut sera remplacée par la version 4.0
À partir de février 2021, la version par défaut du cluster HDInsight passera de la version 3.6 à 4.0. Pour plus d’informations sur les versions disponibles, consultez [Versions disponibles](./hdinsight-component-versioning.md#available-versions). En savoir plus sur les nouveautés de [HDInsight 4.0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Mise à niveau de la version du système d’exploitation
HDInsight met à niveau la version du système d’exploitation de 16.04 à 18.04. La mise à niveau sera terminée avant avril 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Fin de la prise en charge de HDInsight 3.6 le 30 juin 2021
HDInsight 3.6 ne sera plus pris en charge. À partir du 30 juin 2021, les clients ne pourront plus créer de nouveaux clusters HDInsight 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers HDInsight 4.0 pour éviter une éventuelle interruption du système ou du support.

## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 

## <a name="component-version-change"></a>Changement de la version des composants
Aucune modification de la version des composants pour cette version. Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](./hdinsight-component-versioning.md).

## <a name="known-issues"></a>Problèmes connus
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>Empêcher les machines virtuelles du cluster HDInsight de redémarrer régulièrement

Depuis la mi-novembre 2020, vous avez peut-être remarqué que les machines virtuelles du cluster HDInsight sont redémarrées régulièrement. Cela peut être dû aux raisons suivantes :

1.  Clamav est activé sur votre cluster. Le nouveau package azsec-clamav consomme une grande quantité de mémoire qui déclenche le redémarrage du nœud. 
2.  Une tâche CRON est planifiée quotidiennement pour surveiller les modifications apportées à la liste des autorités de certification (CA) utilisées par les services Azure. Lorsqu’un nouveau certificat d’autorité de certification est disponible, le script ajoute le certificat au magasin d’approbations JDK et planifie un redémarrage.

Dans les deux cas, HDInsight déploie des correctifs et applique un patch pour tous les clusters en cours d’exécution. Pour appliquer immédiatement le correctif et éviter le redémarrage inattendu des machines virtuelles, vous pouvez exécuter les actions de script ci-dessous sur tous les nœuds de cluster en tant qu’action de script persistante. HDInsight publiera un autre avis une fois l’application du correctif et du patch terminée.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```