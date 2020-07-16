---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, R Server, Hive et bien plus.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564410"
---
# <a name="release-notes"></a>Notes de publication

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

## <a name="release-date-06112020"></a>Date de publication : 06/11/2020

Cette version s’applique à la fois à HDInsight 3.6 et 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passage à des groupes de machines virtuelles identiques Azure
HDInsight utilise des machines virtuelles Azure pour provisionner le cluster maintenant. À partir de cette version release, les clusters HDInsight nouvellement créés commencent à utiliser le groupe de machines virtuelles identiques Azure. Le changement est déployé progressivement. Vous ne devez pas vous attendre à des changements cassants. En savoir plus sur les [groupes de machines virtuelles identiques Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Redémarrer des machines virtuelles dans le cluster HDInsight
Dans cette version release, nous prenons en charge le redémarrage des machines virtuelles dans le cluster HDInsight pour redémarrer les nœuds qui ne répondent pas. Actuellement, vous ne pouvez le faire que par le biais de l’API. La prise en charge de PowerShell et de l’interface CLI est en cours. Pour plus d’informations sur l’API, consultez [ce document](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json).
 
## <a name="deprecation"></a>Dépréciation
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Dépréciation de Spark 2.1 et 2.2 dans le cluster Spark HDInsight 3.6
À compter du 1er juillet 2020, les clients ne peuvent pas créer des clusters Spark avec Spark 2.1 et 2.2 sur HDInsight 3.6. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Spark 2.3 sur HDInsight 3.6 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Dépréciation de Spark 2.3 dans le cluster Spark HDInsight 4.0
À compter du 1er juillet 2020, les clients ne peuvent pas créer des clusters Spark avec Spark 2.3 sur HDInsight 4.0. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Spark 2.4 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Désapprobation de Kafka 1.1 dans le cluster Kafka HDInsight 4.0
À compter du 1er juillet 2020, les clients ne seront pas en mesure de créer de nouveaux clusters Kafka avec Kafka 1.1 sur HDInsight 4.0. Les clusters existants s’exécuteront tels quels sans le support de Microsoft. Envisagez de migrer vers Kafka 2.1 sur HDInsight 4.0 d’ici le 30 juin 2020 pour éviter une éventuelle interruption du système ou du support.
 
## <a name="behavior-changes"></a>Changements de comportement
### <a name="esp-spark-cluster-head-node-size-change"></a>Changement de la taille du nœud principal de cluster ESP Spark 
La taille de nœud principal minimale autorisée pour le cluster ESP Spark est remplacée par la taille Standard_D13_V2. Les machines virtuelles ayant peu de cœurs et de mémoire comme nœud principal peuvent entraîner des problèmes de cluster ESP en raison d’une capacité de mémoire et de processeur relativement faible. À partir de la version release, utilisez des références SKU supérieures à Standard_D13_V2 et Standard_E16_V3 comme nœud principal pour les clusters ESP Spark.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Une machine virtuelle à au moins quatre cœurs est nécessaire pour le nœud principal 
Une machine virtuelle à au moins 4 cœurs est nécessaire pour le nœud principal, afin de garantir la haute disponibilité et la fiabilité des clusters HDInsight. À compter du 6 avril 2020, les clients peuvent uniquement choisir des machines virtuelles à quatre cœurs ou plus comme nœud principal pour les nouveaux clusters HDInsight. Les clusters existants continueront à s’exécuter comme prévu. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Changement du provisionnement du nœud Worker du cluster
Quand 80 % des nœuds Worker sont prêts, le cluster entre dans la phase **opérationnelle**. Dans cette phase, les clients peuvent effectuer toutes les opérations de plan de données, comme l’exécution de scripts et de travaux. Toutefois, les clients ne peuvent effectuer aucune opération de plan de contrôle comme un scale-up/scale-down. Seule la suppression est prise en charge.
 
Après la phase **opérationnelle**, le cluster attend encore 60 minutes pour les 20 % de nœuds Worker restants. À la fin de ces 60 minutes, le cluster passe à la phase **en cours d’exécution**, même si tous les nœuds Worker ne sont toujours pas disponibles. Une fois qu’un cluster entre dans la phase **en cours d’exécution**, vous pouvez l’utiliser normalement. Les opérations de plan de contrôle comme un scale-up/scale-down aussi bien que les opérations de plan de données comme l’exécution de scripts et de travaux sont acceptées. Si certains des nœuds Worker demandés ne sont pas disponibles, le cluster est marqué comme ayant partiellement réussi. Les nœuds qui ont été correctement déployés vous sont facturés. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Créer un principal du service avec HDInsight
Auparavant, avec la création de cluster, les clients pouvaient créer un principal de service pour accéder au compte ADLS Gen 1 connecté dans le portail Azure. À compter du 15 juin 2020, les clients ne peuvent pas créer de principal de service dans le workflow de création de HDInsight, seul un principal de service existant est pris en charge. Consultez [Créer un principal de service et des certificats à l’aide d’Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

### <a name="time-out-for-script-actions-with-cluster-creation"></a>Délai d’attente pour les actions de script avec création de cluster
HDInsight prend en charge l’exécution d’actions de script avec création de cluster. À partir de cette version release, toutes les actions de script avec création de cluster doivent se terminer dans un délai de **60 minutes**, faute de quoi elles expirent. Les actions de script soumises à des clusters en cours d’exécution ne sont pas impactées. Vous trouverez plus de détails [ici](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process).
 
## <a name="upcoming-changes"></a>Changements à venir
Aucun changement cassant à venir auquel vous devez prêter attention.
 
## <a name="bug-fixes"></a>Résolution des bogues
HDInsight continue à améliorer la fiabilité et les performances des clusters. 
 
## <a name="component-version-change"></a>Changement de la version des composants
### <a name="hbase-20-to-216"></a>HBase 2.0 vers 2.1.6
La version de HBase est mise à niveau de la version 2.0 vers la version 2.1.6.
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 vers 2.4.4
La version de Spark est mise à niveau de la version 2.4.0 vers la version 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 vers 2.1.1
La version de Kafka est mise à niveau de la version 2.1.0 vers la version 2.1.1.
 
Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="known-issues"></a>Problèmes connus

### <a name="hive-warehouse-connector-issue"></a>Problème lié à Hive Warehouse Connector
Il y a un problème concernant Hive Warehouse Connector dans cette version. Le correctif sera inclus dans la prochaine version. Les clusters existants créés avant cette version ne sont pas impactés. Si possible, évitez de supprimer et de recréer le cluster. Si vous avez besoin d’aide supplémentaire à ce sujet, ouvrez un ticket de support.
