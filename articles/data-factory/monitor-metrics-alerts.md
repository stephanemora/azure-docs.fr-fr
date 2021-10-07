---
title: Métriques et alertes Data Factory
description: Découvrez les métriques disponibles pour la supervision d’Azure Data Factory.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 3b8351c94fb7c9ace6dbf0e31a50f2ed3d8789a3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666625"
---
# <a name="data-factory-metrics-and-alerts"></a>Métriques et alertes Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory fournit les métriques et les alertes suivantes pour permettre la supervision du service.

## <a name="data-factory-metrics"></a>Métriques de Data Factory

Monitor vous apporte une visibilité sur les performances et l’intégrité de vos charges de travail Azure. Le type de données d’analyse le plus important de Monitor est la métrique, également appelée compteur de performances. Des métriques sont émises par la plupart des ressources Azure. Monitor propose plusieurs façons de configurer et d’utiliser ces métriques à des fins d’analyse et de résolution des problèmes.

Voici quelques-unes des métriques émises par Azure Data Factory version 2 :

| **Mesure**                           | **Nom d’affichage de la métrique**                  | **Unité** | **Type d’agrégation** | **Description**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Métriques d’exécutions d’activité annulées           | Count    | Total                | Nombre total d’exécutions d’activités annulées dans une fenêtre d’une minute. |
| ActivityFailedRuns                   | Métriques d’exécutions d’activité ayant échoué             | Count    | Total                | Nombre total d’exécutions d’activité ayant échoué dans une fenêtre d’une minute. |
| ActivitySucceededRuns                | Métriques d’exécutions d’activité ayant abouti          | Count    | Total                | Nombre total d’exécutions d’activité ayant réussi dans une fenêtre d’une minute. |
| PipelineCancelledRuns                 | Métriques d’exécutions de pipeline annulées           | Count    | Total                | Nombre total d’exécutions de pipelines annulées dans une fenêtre d’une minute. |
| PipelineFailedRuns                   | Métriques d’exécutions de pipeline ayant échoué             | Count    | Total                | Nombre total d’exécutions de pipeline ayant échoué dans une fenêtre d’une minute. |
| PipelineSucceededRuns                | Métriques d’exécutions de pipeline ayant abouti          | Count    | Total                | Nombre total d’exécutions de pipeline ayant réussi dans une fenêtre d’une minute. |
| TriggerCancelledRuns                  | Métriques d’exécutions de déclencheur annulées            | Count    | Total                | Nombre total d’exécutions de déclencheurs annulées dans une fenêtre d’une minute. |
| TriggerFailedRuns                    | Métriques d’exécutions de déclencheur ayant échoué              | Count    | Total                | Nombre total d’exécutions de déclencheur ayant échoué dans une fenêtre d’une minute. |
| TriggerSucceededRuns                 | Métriques d’exécutions de déclencheur ayant abouti           | Count    | Total                | Nombre total d’exécutions de déclencheur ayant réussi dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStartCancelled  | Métriques de démarrages du runtime d’intégration de SSIS annulés           | Count    | Total                | Nombre total de démarrages du runtime d’intégration de SSIS annulés dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStartFailed    | Métriques de démarrages du runtime d’intégration de SSIS ayant échoué             | Count    | Total                | Nombre total de démarrages du runtime d’intégration de SSIS ayant échoués dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStartSucceeded | Métriques de démarrages du runtime d’intégration de SSIS réussis          | Count    | Total                | Nombre total de démarrages du runtime d’intégration de SSIS ayant réussi dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStopStuck      | Métriques d’arrêts du runtime d’intégration de SSIS bloqués               | Count    | Total                | Nombre total d’arrêts du runtime d’intégration de SSIS bloqués dans une fenêtre d’une minute. |
| SSISIntegrationRuntimeStopSucceeded  | Métriques d’arrêts du runtime d’intégration de SSIS réussis           | Count    | Total                | Nombre total d’arrêts du runtime d’intégration de SSIS ayant réussi dans une fenêtre d’une minute. |
| SSISPackageExecutionCancelled         | Métriques d’exécutions du package SSIS annulées  | Count    | Total                | Nombre total d’exécutions du package SSIS annulées dans une fenêtre d’une minute. |
| SSISPackageExecutionFailed           | Métriques d’exécutions de package SSIS ayant échouées    | Count    | Total                | Nombre total d’exécutions de packages SSIS ayant échoué dans une fenêtre d’une minute. |
| SSISPackageExecutionSucceeded        | Métriques d’exécutions de package SSIS ayant réussies | Count    | Total                | Nombre total d’exécutions de packages SSIS ayant réussi dans une fenêtre d’une minute. |
| PipelineElapsedTimeRuns | Métriques d’exécutions de pipeline - Temps écoulé | Nombre | Total | Dans une fenêtre d’une minute, nombre de fois qu’un pipeline s’exécute plus longtemps que la durée attendue définie par l’utilisateur. [(En savoir plus)](tutorial-operationalize-pipelines.md) |

Pour accéder aux métriques, suivez les instructions fournies dans [Plateforme de données Azure Monitor](../azure-monitor/data-platform.md).

> [!NOTE]
> À l’exception de _PipelineElapsedTimeRuns_, seuls les événements des exécutions de pipeline et d’activité déclenchés et terminés sont émis. Les exécutions en cours et de débogage **ne sont pas** émises. En revanche, les événements de **toutes** les exécutions de packages SSIS sont émis, y compris ceux qui sont terminés et en cours, indépendamment de leurs méthodes d’invocation. Par exemple, vous pouvez invoquer des exécutions de packages sur des outils SQL Server Data Tools (SSDT) compatibles avec Azure, via T-SQL sur SSMS, SQL Server Agent ou d’autres outils désignés, et en tant qu’exécutions déclenchées ou de débogage d’activités d’exécution de package SSIS dans des pipelines ADF.

## <a name="data-factory-alerts"></a>Alertes Data Factory

Connectez-vous au portail Azure, puis sélectionnez **Monitor** > **Alertes** pour créer des alertes.

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="Alertes dans le menu du portail":::

### <a name="create-alerts"></a>Créer des alertes

1. Sélectionnez **+ Nouvelle règle d’alerte** pour créer une nouvelle alerte.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="Nouvelle règle d’alerte":::

1. Définissez la condition de l’alerte.

    > [!NOTE]
    > Veillez à sélectionner **Toutes** dans la liste déroulante **Filtrer par type de ressource**.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="&quot;Définir la condition d’alerte&quot; > &quot;Sélectionner la cible&quot;, qui ouvre le volet &quot;Sélectionner une ressource&quot;":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="&quot;Définir la condition d’alerte&quot; > &quot;Ajouter des critères&quot;, qui ouvre le volet &quot;Configurer la logique du signal&quot;":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="&quot;Volet &quot;Configurer la logique du signal":::

1. Définissez les détails de l’alerte.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="Détails de l'alerte":::

1. Définissez le groupe d’actions.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="Créer une règle, avec &quot;Nouveau groupe d’actions&quot; en surbrillance":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="Créer un nouveau groupe d’action":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="Configurer le courrier, les SMS, les notifications push et la voix":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="Définir un groupe d’actions":::

## <a name="next-steps"></a>Étapes suivantes

[Configurer les paramètres de diagnostic et l’espace de travail](monitor-configure-diagnostics.md)
