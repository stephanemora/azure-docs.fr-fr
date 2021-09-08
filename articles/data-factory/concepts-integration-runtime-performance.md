---
title: Performances Integration Runtime
titleSuffix: Azure Data Factory & Azure Synapse
description: En savoir plus sur la façon d’optimiser et d’améliorer les performances de l’Azure Integration Runtime dans Azure Data Factory et Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: 6cc19a1c37071fc7a9af9c3474e0b706283cdf9d
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116022"
---
# <a name="optimizing-performance-of-the-azure-integration-runtime"></a>Optimisation des performances d’Azure Integration Runtime

Les flux de données s’exécutent sur des clusters Spark qui sont lancés au moment de l’exécution. La configuration du cluster utilisé est définie dans le runtime d’intégration (IR) de l’activité. Trois aspects en lien avec les performances sont à prendre en considération lors de la définition de votre runtime d’intégration : type de cluster, taille de cluster et durée de vie.

Pour plus d’informations sur la création d’un Runtime d’intégration, consultez [Runtime d’intégration](concepts-integration-runtime.md).

## <a name="cluster-type"></a>Type de cluster

Trois options sont disponibles pour le type de cluster Spark démarré : à usage général, à mémoire optimisée et optimisé pour le calcul.

Les clusters **à usage général** sont la sélection par défaut et sont idéaux pour la plupart des charges de travail de flux de données. Ils offrent généralement le meilleur compromis entre performances et coûts.

Si votre flux de données contient de nombreuses jointures et recherches, vous pouvez peut-être utiliser un cluster **à mémoire optimisée**. Les clusters à mémoire optimisée peuvent stocker davantage de données en mémoire et minimiser les erreurs dues à une mémoire insuffisante. Si le niveau de prix par cœur de l’option à mémoire optimisée est le plus élevé, celle-ci tend également à produire des pipelines plus performants. Si vous rencontrez des erreurs dues à une insuffisance de mémoire lors de l’exécution de vos flux de données, basculez vers une configuration d’Azure Integration Runtime à mémoire optimisée. 

L’option **Optimisé pour le calcul** n’est pas idéale pour les flux de travail ETL et est déconseillée pour la plupart des charges de travail de production. Pour des transformations de données plus simples n’utilisant pas beaucoup de mémoire, telles que le filtrage de données ou l’ajout de colonnes dérivées, des clusters optimisés pour le calcul permettent d’abaisser le coût par cœur.

## <a name="cluster-size"></a>Taille du cluster

Les flux de données répartissent le traitement des données sur différents nœuds d’un cluster Spark pour effectuer des opérations en parallèle. Un cluster Spark avec davantage de cœurs augmente le nombre de nœuds dans l’environnement Compute. Des nœuds en plus grand nombre augmentent la puissance de traitement du flux de données. L’augmentation de la taille du cluster est souvent un moyen simple de réduire le temps de traitement.

La taille de cluster par défaut est de quatre nœuds de pilote et quatre nœuds worker.  Lorsque vous traitez davantage de données, des clusters plus grands sont recommandés. Vous trouverez ci-dessous les options de dimensionnement possibles :

| Cœurs de worker | Cœurs de pilote | Nombre total de cœurs | Notes |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Non disponible pour l’option optimisé pour le calcul |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Les flux de données sont tarifés sur la base de vCores/heure, mode de calcul intégrant la taille du cluster et le facteur de temps d’exécution. Lorsque vous augmentez l’échelle, le coût par minute de votre cluster augmente, mais le temps global d’exécution diminue.

> [!TIP]
> Il existe un plafond à la manière dont la taille d’un cluster affecte les performances d’un flux de données. Selon la taille de vos données, il existe un point au-delà duquel l’augmentation de la taille d’un cluster n’améliore plus les performances. Par exemple, si vous avez plus de nœuds que de partitions de données, l’ajout de nœuds n’est pas utile. Une bonne pratique consiste à commencer petit et à augmenter l’échelle pour répondre à vos besoins en matière de performances. 

## <a name="time-to-live"></a>Durée de vie

Par défaut, chaque activité de flux de données a pour effet de démarrer un nouveau cluster Spark en fonction de la configuration d’Azure IR. Le démarrage des clusters froids prend quelques minutes et le traitement des données ne peut pas commencer tant que le processus de démarrage n’est pas terminé. Si vos pipelines contiennent plusieurs flux de données **séquentiels**, vous pouvez activer une valeur de durée de vie (TTL). La spécification d’une valeur de durée de vie a pour effet que le cluster reste actif pendant un certain temps après la fin de son exécution. Si un nouveau travail commence à utiliser le runtime d’intégration (IR) pendant la durée de vie (TTL), il va réutiliser le cluster existant et le temps de démarrage sera donc fortement réduit. Une fois le deuxième travail terminé, le cluster reste actif pendant la durée de vie.

Vous pouvez également réduire au minimum le temps de démarrage des clusters chauds en définissant l’option « Réutilisation rapide » dans le runtime d’intégration Azure sous Propriétés du flux de données. Si vous définissez cette option sur true, le service ne supprimera pas le cluster existant après chaque tâche et le réutilisera à la place, ce qui permet de maintenir actif l’environnement de calcul que vous avez défini dans votre IR Azure jusqu’à la fin de la période spécifiée dans votre TTL. Cette option permet de réduire au maximum le temps de démarrage de vos activités de flux de données lorsqu’elles sont exécutées à partir d’un pipeline.

Toutefois, si la plupart de vos flux de données s’exécutent en parallèle, il n’est pas recommandé d’activer la TTL de l’IR que vous utilisez pour ces activités. Une seule tâche peut être exécutée sur un seul cluster à la fois. Si un cluster est disponible, mais que deux flux de données démarrent, un seul utilise par le cluster actif. Le deuxième travail démarre son propre cluster isolé.

> [!NOTE]
> La durée de vie n’est pas disponible lors de l’utilisation du runtime d’intégration de résolution automatique.

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres articles sur les flux de données consacrés aux performances :

- [Performances de flux de données](concepts-data-flow-performance.md)
- [Activité Data Flow](control-flow-execute-data-flow-activity.md)
- [Analyser les performances des flux de données](concepts-data-flow-monitoring.md)
