---
title: Guide des performances et de réglage du flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez les facteurs clés ayant une incidence sur les performances des flux de données de mappage dans Azure Data Factory et les pipelines Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7f12eb06d514ddc7d001012b0e3111c7603bdca9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274042"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guide des performances et du réglage du mappage de flux de données

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Les flux de données de mappage dans les pipelines Azure Data Factory et Synapse fournissent une interface sans code pour concevoir et exécuter des transformations de données à grande échelle. Si vous n’êtes pas familiarisé avec les flux de données de mappage, consultez [Vue d’ensemble des flux de données de mappage](concepts-data-flow-overview.md). Cet article met en évidence différentes façons de régler et d’optimiser vos flux de données afin qu’ils soient conformes à vos points de référence en matière de performances.

Regardez la vidéo ci-dessous pour voir des exemples de minutages qui transforment les données avec des flux de données.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Supervision des performances de flux de données

Une fois que vous avez vérifié votre logique de transformation à l’aide du mode débogage, exécutez votre flux de travail de bout en bout en tant qu’activité dans un pipeline. Les flux de données sont mis en œuvre dans un pipeline à l’aide de l’[activité d’exécution d’un flux de données](control-flow-execute-data-flow-activity.md). L’activité de flux de données offre une expérience de surveillance unique par rapport aux autres activités qui affichent un plan d’exécution détaillé et un profil de performances de la logique de transformation. Pour afficher des informations de surveillance détaillées d’un flux de données, cliquez sur l’icône de lunettes dans la sortie d’exécution de l’activité d’un pipeline. Pour plus d’informations, consultez [Supervision des flux de données de mappage](concepts-data-flow-monitoring.md).

:::image type="content" source="media/data-flow/monitoring-details.png" alt-text="Moniteur Data Flow":::

Lors de la surveillance des performances du flux de données, quatre goulots d’étranglement peuvent se présenter :

* temps de démarrage du cluster ;
* lecture à partir d’une source ;
* temps de transformation ;
* écriture dans un récepteur. 

:::image type="content" source="media/data-flow/monitoring-performance.png" alt-text="Surveillance du flux de données":::

Le temps de démarrage du cluster est le temps nécessaire au lancement d’un cluster Apache Spark. Cette valeur se trouve dans l’angle supérieur droit de l’écran de surveillance. Les flux de données s’exécutent selon un modèle juste-à-temps, où chaque travail utilise un cluster isolé. Ce temps de démarrage est généralement de 3 à 5 minutes. Pour des travaux séquentiels, il est possible de réduire ce temps en activant une valeur de durée de vie. Pour plus d’informations, reportez-vous à la section **Durée de vie** dans [Performance du runtime d’intégration](concepts-integration-runtime-performance.md#time-to-live).

Les flux de données utilisent un optimiseur Spark qui réorganise et exécute votre logique métier par « étapes » afin d’accélérer le processus. Pour chaque récepteur dans lequel votre flux de données écrit, la sortie de la surveillance indique la durée de chaque étape de transformation, ainsi que le temps nécessaire pour écrire les données dans le récepteur. La durée la plus important correspond probablement au goulot d’étranglement de votre flux de données. Si l’étape de transformation qui prend le plus de temps contient une source, vous souhaiterez peut-être optimiser davantage votre temps de lecture. Si une transformation prend beaucoup de temps, il se peut que vous deviez repartitionner ou augmenter la taille de votre runtime d’intégration. Si la durée de traitement du récepteur est importante, il se peut que vous deviez augmenter l’échelle de votre base de données ou vérifier que la sortie ne s’effectue pas dans un seul fichier.

Une fois que vous avez identifié le goulot d’étranglement de votre flux de données, utilisez les stratégies d’optimisation ci-dessous pour améliorer les performances.

## <a name="testing-data-flow-logic"></a>Test de la logique de flux de données

Lorsque vous concevez et testez des flux de données à partir de l’expérience utilisateur, le mode débogage vous permet de tester de manière interactive un cluster Spark en direct. Cela vous permet d’afficher un aperçu des données et d’exécuter vos flux de données sans attendre le préchauffage d’un cluster. Pour plus d’informations, consultez [Mode de débogage](concepts-data-flow-debug-mode.md).

## <a name="optimize-tab"></a>Onglet Optimiser

L’onglet **Optimiser** contient des paramètres pour configurer le schéma de partitionnement du cluster Spark. Cet onglet présent dans chaque transformation de flux de données spécifie si vous souhaitez repartitionner les données **après** la transformation. L’ajustement du partitionnement permet de contrôler la répartition de vos données entre les nœuds de calcul et les optimisations de la localisation des données qui peuvent avoir des effets tant positifs que négatifs sur les performances globales de vos flux de données.

:::image type="content" source="media/data-flow/optimize.png" alt-text="La capture d’écran montre l’onglet Optimiser, qui comprend l’option Partition, Type de partition et Nombre de partitions.":::

Par défaut, l’option *Utiliser le partitionnement actuel* est sélectionnée, laquelle indique au service de conserver le partitionnement de sortie actuel de la transformation. Le repartitionnement des données prenant du temps, est recommandé d’*utiliser le partitionnement actuel* dans la plupart des scénarios. Les scénarios dans lesquels il peut être souhaitable de repartitionner vos données incluent des agrégats et des jointures qui entraînent une asymétrie considérable de vos données, ou l’utilisation d’un partitionnement de source sur une base de données SQL.

Pour changer le partitionnement de n’importe quelle transformation, sélectionnez l’onglet **Optimiser**, puis la case d’option **Définir le partitionnement**. Une série d'options de partitionnement vous est présentée. La méthode de partitionnement qui convient varie en fonction des volumes de données, des clés candidates, des valeurs null et de la cardinalité. 

> [!IMPORTANT]
> Une partition unique combine toutes les données distribuées dans une seule partition. Il s’agit d’une opération très lente qui affecte considérablement toutes les transformations et écritures en aval. Cette option est fortement déconseillée à moins qu’il y ait une raison commerciale explicite de l’utiliser.

Les options de partitionnement suivantes sont disponibles dans chaque transformation :

### <a name="round-robin"></a>Tourniquet 

Un tourniquet (round robin) répartit équitablement les données entre les partitions. Utilisez un tourniquet (round robin) lorsque vous ne disposez pas des clés candidates adéquates pour implémenter une stratégie de partitionnement solide et intelligente. Vous pouvez définir le nombre de partitions physiques.

### <a name="hash"></a>Hachage

Le service produit un hachage de colonnes pour produire des partitions uniformes, de sorte que des lignes contenant des valeurs similaires tombent dans la même partition. Lorsque vous utilisez l’option Hachage, effectuez un test pour détecter une éventuelle inclinaison de partition. Vous pouvez définir le nombre de partitions physiques.

### <a name="dynamic-range"></a>Plage dynamique

La plage dynamique utilise des plages dynamiques Spark basées sur les colonnes ou expressions que vous fournissez. Vous pouvez définir le nombre de partitions physiques. 

### <a name="fixed-range"></a>Plage fixe

Créez une expression qui fournit une plage fixe pour les valeurs figurant dans vos colonnes de données partitionnées. Pour éviter une inclinaison de partition, vous devez avoir une bonne compréhension de vos données avant d’utiliser cette option. Les valeurs que vous entrez pour l'expression sont utilisées dans le cadre d'une fonction de partition. Vous pouvez définir le nombre de partitions physiques.

### <a name="key"></a>Clé

Si vous avez une bonne compréhension de la cardinalité de vos données, une clé de partitionnement peut être une bonne stratégie. La clé de partitionnement crée des partitions pour chaque valeur unique de votre colonne. Vous ne pouvez pas définir le nombre de partitions car celui-ci dépend des valeurs uniques figurant dans les données.

> [!TIP]
> La définition manuelle du schéma de partitionnement a pour effet de remanier les données et risque d’annihiler les avantages de l’optimiseur Spark. La meilleure pratique consiste à ne pas définir manuellement le partitionnement, sauf si c’est indispensable.

## <a name="logging-level"></a>Niveau de journalisation

Si vous n’avez pas besoin que chaque exécution du pipeline de vos activités de flux de données journalise entièrement tous les journaux de télémétrie détaillés, vous pouvez éventuellement définir le niveau de journalisation sur « De base » ou « Aucun ». Lors de l’exécution de vos flux de données en mode « Verbose » (par défaut), vous demandez au service d’enregistrer entièrement l’activité à chaque niveau de partition individuel au cours de la transformation des données. Cela peut être une opération coûteuse. Par conséquent, n’activez l’option Verbose que lorsque la résolution des problèmes peut améliorer les performances globales du pipeline et du flux de données. Le mode « De base » ne consigne que les durées de transformation, tandis que le mode « Aucun » ne fournit qu’un résumé des durées.

:::image type="content" source="media/data-flow/logging.png" alt-text="Niveau de journalisation":::

## <a name="next-steps"></a>Étapes suivantes

- [Optimisation des sources](concepts-data-flow-performance-sources.md)
- [Optimisation des récepteurs](concepts-data-flow-performance-sinks.md)
- [Optimisation des transformations](concepts-data-flow-performance-transformations.md)
- [Utilisation des flux de données dans des pipelines](concepts-data-flow-performance-pipelines.md)

Consultez d’autres articles sur les flux de données consacrés aux performances :

- [Activité Data Flow](control-flow-execute-data-flow-activity.md)
- [Analyser les performances des flux de données](concepts-data-flow-monitoring.md)
- [Performances Integration Runtime](concepts-integration-runtime-performance.md)
