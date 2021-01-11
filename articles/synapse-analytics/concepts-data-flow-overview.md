---
title: Flux de données
description: Présentation du flux de données dans Azure Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: references_regions
ms.date: 12/16/2020
ms.openlocfilehash: 18322afbdca94b12ef142f6e37c4d2a22170436b
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592545"
---
# <a name="data-flows-in-azure-synapse-analytics"></a>Flux de données dans Azure Synapse Analytics

## <a name="what-are-data-flows"></a>Présentation des flux de données

Les flux de données sont des transformations de données conçues de manière graphique dans Azure Synapse Analytics. Les flux de données permettent aux ingénieurs de données de développer une logique de transformation des données sans rédiger de code. Les flux de données qui en résultent sont exécutés en tant qu’activités dans les pipelines Azure Synapse Analytics qui utilisent des clusters Apache Spark faisant l’objet d’un scale-out. Les activités de flux de données peuvent être mises en œuvre à l’aide de fonctionnalités de planification, de contrôle, de flux et de supervision Azure Synapse Analytics existantes.

Les flux de données fournissent une expérience entièrement visuelle sans aucun codage. Vos flux de données sont exécutés sur les clusters d’exécution gérés par Synapse pour un traitement des données faisant l’objet d’un scale-out. Azure Synapse Analytics gère intégralement la traduction du code, l’optimisation du chemin et l’exécution de vos travaux de flux de données.

## <a name="getting-started"></a>Prise en main

Les flux de données sont créés dans le volet de développement dans Synapse Studio. Pour créer un flux de données, sélectionnez le signe plus (+) en regard de **Développer**, puis sélectionnez **Flux de données**. 

![Nouveau flux de données](media/data-flow/new-data-flow.png)

Vous accédez au canevas du flux de données dans lequel vous pouvez créer votre logique de transformation. Sélectionnez **Ajouter une source** pour commencer à configurer votre transformation de la source. Pour plus d’informations, consultez [Transformation de la source](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="authoring-data-flows"></a>Création de flux de données

Le flux de données a un canevas de création unique conçu pour faciliter la création de logique de transformation. Le canevas de flux de données est divisé en trois parties : la barre supérieure, le graphe et le panneau de configuration. 

![Capture d’écran montrant le canevas du flux de données avec la barre supérieure, le graphique et le panneau de configuration étiquetés.](media/data-flow/canvas-1.png)

### <a name="graph"></a>Graph

Le graphe affiche le flux de transformation. Il montre la traçabilité des données sources à mesure qu’elles sont transmises à un ou plusieurs récepteurs. Pour ajouter une nouvelle source, sélectionnez **Ajouter une source**. Pour ajouter une nouvelle transformation, sélectionnez le signe plus (+) situé dans la partie inférieure droite d’une transformation existante. Apprenez à [gérer le graphique des flux de données](../data-factory/concepts-data-flow-manage-graph.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Capture d’écran montrant la partie graphique du canevas avec une zone de texte de recherche.](media/data-flow/canvas-2.png)

### <a name="configuration-panel"></a>Panneau de configuration

Le panneau de configuration affiche les paramètres spécifiques à la transformation actuellement sélectionnée. Si aucune transformation n’est sélectionnée, le flux de données est affiché. Dans la configuration globale du flux de données, vous pouvez ajouter des paramètres via l’onglet **Paramètres**. Pour plus d’informations, consultez [Paramètres de flux de données](../data-factory/parameters-data-flow.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Chaque transformation contient au moins quatre onglets de configuration.

#### <a name="transformation-settings"></a>Paramètres de transformation

Le premier onglet du volet de configuration de chaque transformation contient les paramètres spécifiques à cette transformation. Pour plus d’informations, reportez-vous à la page de documentation de cette transformation.

![Onglet Paramètres de la source](media/data-flow/source-1.png)

#### <a name="optimize"></a>Optimiser

L’onglet **Optimiser** contient des paramètres pour configurer des schémas de partitionnement. Pour en savoir plus sur l’optimisation de vos flux de données, consultez le [Guide des performances de flux de données de mappage](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

![Capture d’écran montrant l’onglet Optimisation](media/data-flow/optimize.png)

#### <a name="inspect"></a>Inspecter

Le volet **Inspecter** permet de visualiser les métadonnées du flux de données que vous êtes en train de transformer. Vous pouvez voir le nombre de colonnes, les colonnes modifiées, les colonnes ajoutées, les types de données, l'ordre des colonnes et les références des colonnes. **Inspecter** est un affichage en lecture seule de vos métadonnées. Il n’est pas nécessaire que le mode de débogage soit activé pour voir les métadonnées dans le volet **Inspecter**.

![Onglet Inspecter](media/data-flow/inspect.png)

À mesure que vous modifiez la forme de vos données par le biais de transformations, les changements de métadonnées sont visibles dans le volet **Inspecter**. Si votre transformation de la source ne comporte pas de schéma défini, les métadonnées ne sont pas visibles dans le volet **Inspecter**. L’absence de métadonnées est fréquent dans les scénarios de dérive de schéma.

#### <a name="data-preview"></a>Aperçu des données

Si le mode de débogage est activé, l’onglet **Aperçu des données**  vous donne une capture instantanée interactive des données à chaque transformation. Pour en savoir plus, consultez [Aperçu des données en mode de débogage](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#data-preview).

### <a name="top-bar"></a>Barre supérieure

La barre supérieure contient des actions qui affectent l’ensemble du flux de données, comme les paramètres de validation et de débogage. Vous pouvez également afficher le code JSON sous-jacent et le script de flux de données de votre logique de transformation.

## <a name="available-transformations"></a>Transformations disponibles

Pour obtenir la liste des transformations disponibles, consultez la [Vue d’ensemble de la transformation de flux de données de mappage](../data-factory/data-flow-transformation-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="data-flow-activity"></a>Activité de flux de données

Les flux de données sont mis en œuvre dans les pipelines Azure Synapse Analytics à l’aide de l’[activité de flux de données](../data-factory/control-flow-execute-data-flow-activity.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Il suffit à l’utilisateur de spécifier le runtime d’intégration à utiliser et de passer des valeurs de paramètre. Pour plus d’informations, consultez [Runtime d’intégration Azure](../data-factory/concepts-integration-runtime.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#azure-integration-runtime).

## <a name="debug-mode"></a>Mode débogage

Le mode débogage vous permet de voir de manière interactive les résultats de chaque étape de transformation pendant que vous générez et déboguez vos flux de données. La session de débogage peut être utilisée à la fois dans lors de la génération de votre logique de flux de données et l’exécution des exécutions de débogage de pipeline avec les activités de flux de données. Pour plus d’informations, consultez la [documentation relative au mode de débogage](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="monitoring-data-flows"></a>Surveillance des flux de données

Le flux de données s’intègre aux fonctionnalités d’analyse existantes d’Azure Synapse Analytics. Pour savoir comment comprendre la sortie de la surveillance du flux de données, consultez [Supervision des flux de données de mappage](../data-factory/concepts-data-flow-monitoring.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

L’équipe Azure Synapse Analytics a créé un [guide de réglage des performances](../data-factory/concepts-data-flow-performance.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) pour vous aider à optimiser le temps d’exécution de vos flux de données après avoir généré votre logique métier.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment créer une [transformation de la source](../data-factory/data-flow-source.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Découvrez comment créer vos flux de données en [mode de débogage](../data-factory/concepts-data-flow-debug-mode.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
