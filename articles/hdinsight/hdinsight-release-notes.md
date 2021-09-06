---
title: Notes de publication pour Azure HDInsight
description: Dernières notes de publication pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Hadoop, Spark, Hive et bien plus.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: cf726033a90e256a2d41a3249e237f5eb91aee84
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531844"
---
# <a name="azure-hdinsight-release-notes"></a>Notes de publication Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Résumé

Azure HDInsight est l’un des services les plus populaires parmi les clients d’entreprise pour l’analytique open source sur Azure.

Si vous souhaitez vous abonner aux notes de publication, regardez les communiqués sur [ce référentiel GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-07272021"></a>Date de publication : 27/07/2021

Cette version s’applique à la fois à HDInsight 3.6 et HDInsight 4.0. La version HDInsight est mise à disposition dans toutes les régions sur plusieurs jours. La date de publication mentionnée ici indique la date de publication dans la première région. Si vous ne voyez pas les changements ci-dessous, attendez quelques jours pour que la version release soit active dans votre région.

Les versions de système d’exploitation pour cette mise en production sont les suivantes :
- HDInsight 3.6 : Ubuntu 16.04.7 LTS
- HDInsight 4.0 : Ubuntu 18.04.5 LTS

## <a name="new-features"></a>Nouvelles fonctionnalités
### <a name="new-azure-monitor-integration-experience-preview"></a>Nouvelle expérience d’intégration d’Azure Monitor (préversion)
La nouvelle expérience d’intégration d’Azure Monitor sera en préversion dans les régions USA Est et Europe Ouest avec cette version. Découvrez-en plus sur la nouvelle expérience d’Azure Monitor [ici](./log-analytics-migration.md#migrate-to-the-new-azure-monitor-integration).

## <a name="deprecation"></a>Dépréciation
### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Support de base pour HDInsight 3.6 à partir du 1er juillet 2021
À partir du 1er juillet 2021, Microsoft propose un [Support de base](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) pour certains types de cluster HDInsight 3.6. Le plan de Support de base sera disponible jusqu’au 3 avril 2022. Vous êtes automatiquement inscrit au Support de base à partir du 1er juillet 2021. Aucune action n’est requise pour vous inscrire. Consultez [notre documentation](hdinsight-36-component-versioning.md) sur les types de clusters inclus dans le Support de base. 

Nous vous déconseillons de créer de nouvelles solutions sur HDInsight 3.6, figez les modifications sur les environnements 3.6 existants. Nous vous recommandons de [migrer vos clusters vers HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). En savoir plus sur [les nouveautés de HDInsight 4.0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="behavior-changes"></a>Changements de comportement
### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>La requête interactive HDInsight prend uniquement en charge la mise à l’échelle automatique basée sur une planification
À mesure que les scénarios client augmentent et se diversifient, nous avons identifié certaines limitations avec la mise à l’échelle automatique basée sur la charge LLAP (Interactive Query). Ces limitations sont dues à la nature de la dynamique des requêtes LLAP, aux problèmes de précision de la prédiction de la charge future et aux problèmes dans la redistribution des tâches du planificateur LLAP. En raison de ces limitations, les utilisateurs peuvent voir leurs requêtes s’exécuter plus lentement sur les clusters LLAP lorsque la mise à l’échelle automatique est activée. L’impact sur les performances peut être plus important que le coût de la mise à l’échelle automatique.

À partir de juillet 2021, la charge de travail Interactive Query dans HDInsight prend uniquement en charge la mise à l’échelle automatique basée sur la planification. Vous ne pouvez plus activer la mise à l’échelle automatique basée sur la charge sur les nouveaux clusters Interactive Query. Les clusters en cours d’exécution existants peuvent continuer à s’exécuter avec les limitations connues décrites ci-dessus. 

Microsoft vous recommande de passer à une mise à l’échelle automatique basée sur une planification pour LLAP.  Vous pouvez analyser le modèle d’utilisation actuel de votre cluster via le tableau de bord Grafana Hive. Pour plus d’informations, consultez [Mettre à l’échelle automatiquement les clusters Azure HDInsight](hdinsight-autoscale-clusters.md). 

## <a name="upcoming-changes"></a>Changements à venir
Les changements suivants se produiront dans les prochaines versions.

### <a name="built-in-llap-component-in-esp-spark-cluster-will-be-removed"></a>Le composant LLAP intégré dans le cluster ESP Spark sera supprimé
Le cluster HDInsight 4.0 ESP Spark contient des composants LLAP intégrés s’exécutant sur les deux nœuds principaux. Les composants LLAP du cluster ESP Spark ont été ajoutés à l’origine pour HDInsight 3.6 ESP Spark, mais n’ont pas de cas utilisateur réel pour HDInsight 4.0 ESP Spark. Dans la prochaine version prévue pour septembre 2021, HDInsight supprimera le composant LLAP intégré du cluster HDInsight 4.0 ESP Spark. Cette modification permet de décharger la charge de travail du nœud principal et d’éviter les confusions entre ESP Spark et le type de cluster ESP Interactive Hive.

## <a name="new-region"></a>Nouvelle région
- USA Ouest 3
- Inde Ouest Jio
- Centre de l’Australie

## <a name="component-version-change"></a>Changement de la version des composants
La version du composant suivante a été modifiée avec cette version :
- ORC version 1.5.1 à 1.5.9

Les versions actuelles des composants pour HDInsight 4.0 et HDInsight 3.6 sont indiquées dans [ce document](./hdinsight-component-versioning.md).

## <a name="back-ported-jiras"></a>JIRA rétroportés
Les JIRA Apache rétroportés pour cette version sont les suivants :

| Fonctionnalité affectée    |   Apache JIRA                                                      |
|---------------------|--------------------------------------------------------------------|
| Date / Timestamp    | [HIVE-25104](https://issues.apache.org/jira/browse/HIVE-25104)     |
|                     | [HIVE-24074](https://issues.apache.org/jira/browse/HIVE-24074)     |
|                     | [HIVE-22840](https://issues.apache.org/jira/browse/HIVE-22840)     |
|                     | [HIVE-22589](https://issues.apache.org/jira/browse/HIVE-22589)     |
|                     | [HIVE-22405](https://issues.apache.org/jira/browse/HIVE-22405)     |
|                     | [HIVE-21729](https://issues.apache.org/jira/browse/HIVE-21729)     |
|                     | [HIVE-21291](https://issues.apache.org/jira/browse/HIVE-21291)     |
|                     | [HIVE-21290](https://issues.apache.org/jira/browse/HIVE-21290)     |
| Fonctions définies par l'utilisateur                 | [HIVE-25268](https://issues.apache.org/jira/browse/HIVE-25268)     |
|                     | [HIVE-25093](https://issues.apache.org/jira/browse/HIVE-25093)     |
|                     | [HIVE-22099](https://issues.apache.org/jira/browse/HIVE-22099)     |
|                     | [HIVE-24113](https://issues.apache.org/jira/browse/HIVE-24113)     |
|                     | [HIVE-22170](https://issues.apache.org/jira/browse/HIVE-22170)     |
|                     | [HIVE-22331](https://issues.apache.org/jira/browse/HIVE-22331)     |
| ORC                 | [HIVE-21991](https://issues.apache.org/jira/browse/HIVE-21991)     |
|                     | [HIVE-21815](https://issues.apache.org/jira/browse/HIVE-21815)     |
|                     | [HIVE-21862](https://issues.apache.org/jira/browse/HIVE-21862)     |
| Schéma de table        | [HIVE-20437](https://issues.apache.org/jira/browse/HIVE-20437)     |
|                     | [HIVE-22941](https://issues.apache.org/jira/browse/HIVE-22941)     |
|                     | [HIVE-21784](https://issues.apache.org/jira/browse/HIVE-21784)     |
|                     | [HIVE-21714](https://issues.apache.org/jira/browse/HIVE-21714)     |
|                     | [HIVE-18702](https://issues.apache.org/jira/browse/HIVE-18702)     |
|                     | [HIVE-21799](https://issues.apache.org/jira/browse/HIVE-21799)     |
|                     | [HIVE-21296](https://issues.apache.org/jira/browse/HIVE-21296)     |
| Gestion des charges de travail | [HIVE-24201](https://issues.apache.org/jira/browse/HIVE-24201)     |
| Compactage          | [HIVE-24882](https://issues.apache.org/jira/browse/HIVE-24882)     |
|                     | [HIVE-23058](https://issues.apache.org/jira/browse/HIVE-23058)     |
|                     | [HIVE-23046](https://issues.apache.org/jira/browse/HIVE-23046)     |
| Vue matérialisée   | [HIVE-22566](https://issues.apache.org/jira/browse/HIVE-22566)     |