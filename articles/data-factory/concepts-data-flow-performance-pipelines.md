---
title: Optimisation des performances du pipeline dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: En savoir plus sur l’optimisation de l’exécution des flux de données dans les pipelines dans les pipelines Azure Data Factory et Azure Synapse Analytics.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 3545d7e8bbf8131c9fe454b39ea57a23cd233264
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293575"
---
# <a name="using-data-flows-in-pipelines"></a>Utilisation des flux de données dans des pipelines 

Lors de la création de pipelines complexes avec plusieurs flux de données, votre flux logique peut avoir un impact important sur le temps et le coût. Cette section décrit l’impact de différentes stratégies d’architecture.

## <a name="executing-data-flows-in-parallel"></a>Exécuter des flux de données en parallèle

Si vous exécutez plusieurs flux de données en parallèle, le service crée des clusters Spark distincts pour chaque activité. Cela permet d’isoler chaque travail et de l’exécuter en parallèle, mais cela entraîne l’exécution simultanée de plusieurs clusters.

Si vos flux de données s’exécutent en parallèle, nous vous recommandons de ne pas activer la propriété de durée de vie Azure IR, car elle entraînerait l’utilisation de plusieurs pools à chaud inutilisés.

> [!TIP]
> Au lieu d’exécuter le même flux de données plusieurs fois pour chaque activité, mettez vos données dans un lac de données et utilisez des chemins d’accès génériques pour traiter les données dans un seul et même flux de données.

## <a name="execute-data-flows-sequentially"></a>Exécuter des flux de données séquentiellement

Si vous exécutez vos activités de flux de données dans l’ordre, il est recommandé de définir une durée de vie dans la configuration d’Azure IR. Le service réutilise les ressources de calcul, ce qui accélère le démarrage du cluster. Chaque activité sera toujours isolée et recevra un nouveau contexte Spark pour chaque exécution. Pour réduire encore davantage le temps entre les activités séquentielles, cochez la case **Réutiliser rapidement** sur Azure IR pour indiquer au service de réutiliser le cluster existant.

## <a name="overloading-a-single-data-flow"></a>Surcharge d’un seul flux de données

Si vous placez toute votre logique au sein d’un même flux de données, le service exécutera l’intégralité du travail sur une seule instance Spark. Bien que cela puisse sembler être un moyen de réduire les coûts, cette approche combine des flux logiques différents et peut être difficile à surveiller et à déboguer. En cas de défaillance d’un composant, toutes les autres parties du travail échouent également. L’organisation des flux de données par des flux indépendants de logique commerciale est recommandée. Si votre flux de données devient trop volumineux, le fractionnement en composants distincts facilite la surveillance et le débogage. Bien qu’il n’y ait pas de limite inconditionnelle sur le nombre de transformations dans un flux de données, un trop grand nombre de transformations rend le travail complexe.

## <a name="execute-sinks-in-parallel"></a>Exécuter des récepteurs en parallèle

Le comportement par défaut des récepteurs de flux de données consiste à exécuter chaque récepteur de manière séquentielle, en série, et à faire échouer le flux quand une erreur est détectée dans le récepteur. En outre, tous les récepteurs sont définis par défaut dans le même groupe, sauf si vous accédez aux propriétés du flux de données et définissez des priorités différentes pour les récepteurs.

Les flux de données vous permettent de regrouper des récepteurs dans des groupes à partir de l’onglet des propriétés des flux de données dans le concepteur d’interface utilisateur. Vous pouvez définir l’ordre d’exécution de vos récepteurs et regrouper les récepteurs en utilisant le même numéro de groupe. Pour faciliter la gestion des groupes, vous pouvez demander au service d’exécuter des récepteurs dans le même groupe pour qu’ils s’exécutent en parallèle.

Dans l’activité Exécuter le flux de données du pipeline, sous la section « Propriétés du récepteur », vous avez la possibilité d’activer le chargement parallèle des récepteurs. Lorsque vous activez « exécuter en parallèle », vous donnez aux flux de données l’instruction d’écrire sur les récepteurs connectés en même temps plutôt que de manière séquentielle. Pour pouvoir utiliser l’option d’exécution en parallèle, les récepteurs doivent être regroupés et connectés au même flux via une nouvelle branche ou un nouveau fractionnement conditionnel.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des performances de flux de données](concepts-data-flow-performance.md)
- [Optimisation des sources](concepts-data-flow-performance-sources.md)
- [Optimisation des récepteurs](concepts-data-flow-performance-sinks.md)
- [Optimisation des transformations](concepts-data-flow-performance-transformations.md)

Consultez d’autres articles sur les flux de données consacrés aux performances :

- [Activité Data Flow](control-flow-execute-data-flow-activity.md)
- [Analyser les performances des flux de données](concepts-data-flow-monitoring.md)
- [Performances Integration Runtime](concepts-integration-runtime-performance.md)
