---
title: Configurer les paramètres de diagnostic et l’espace de travail
description: Découvrez comment configurer des paramètres de diagnostic et un espace de travail Log Analytics pour analyser Azure Data Factory.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 5673dd881d96dc14922e8a9f9bedc0501d2f5d18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838518"
---
# <a name="configure-diagnostic-settings-and-workspace"></a>Configurer les paramètres de diagnostic et l’espace de travail

Créez ou ajoutez des paramètres de diagnostic pour votre fabrique de données.

1. Dans le portail, accédez à Monitor. Sélectionnez **Paramètres** > **Paramètres de diagnostic**.

1. Sélectionnez la fabrique de données pour laquelle vous souhaitez définir un paramètre de diagnostic.

1. S’il n’existe aucun paramètre sur la fabrique de données sélectionnée, vous êtes invité à en créer un. Sélectionnez **Activer les diagnostics**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="Créer un paramètre de diagnostic s’il n’en existe pas":::

   S’il existe des paramètres sur la fabrique de données, vous voyez une liste de paramètres déjà configurés sur cette fabrique de données. Sélectionnez **Ajouter le paramètre de diagnostic**.

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="Ajouter un paramètre de diagnostic s’il existe des paramètres":::

1. Nommez votre paramètre, sélectionnez **Envoyer à log Analytics**, puis sélectionnez un espace de travail dans **Espace de travail Log Analytics**.

    * En mode _Diagnostics Azure_, les journaux de diagnostic circulent dans la table _AzureDiagnostics_.

    * En mode _Spécifique à la ressource_, les journaux de diagnostic d’Azure Data Factory circulent dans les tables suivantes :
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Vous pouvez sélectionner différents journaux pertinents pour vos charges de travail à envoyer vers les tableaux Log Analytics. Par exemple, si vous n’utilisez pas du tout SQL Server Integration Services (SSIS), vous n’avez pas besoin de sélectionner des journaux SSIS. Si vous souhaitez consigner les opérations de démarrage/arrêt et de maintenance du SSIS Integration Runtime (IR), vous pouvez sélectionner les journaux SSIS IR. Si vous appelez des exécutions de package SSIS via T-SQL sur SQL Server Management Studio (SSMS), SQL Server Agent ou d’autres outils désignés, vous pouvez sélectionner des journaux de packages SSIS. Si vous appelez des exécutions de package SSIS via des activités Exécuter le package SSIS dans des pipelines ADF, vous pouvez sélectionner tous les journaux.

    * Si vous sélectionnez _AllMetrics_, plusieurs métriques ADF sont mises à votre disposition pour surveiller ou déclencher des alertes, y compris les métriques pour l’activité ADF, le pipeline et les exécutions de déclencheur, ainsi que pour les opérations de runtime d’intégration de SSIS et les exécutions de packages SSIS.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="Nommer vos paramètres et sélectionner un espace de travail Log Analytics":::

    > [!NOTE]
    > Étant donné qu’une table de journal Azure ne peut pas comporter plus de 500 colonnes, nous vous **recommandons vivement** de sélectionner le _mode spécifique de la ressource_. Pour plus d’informations, consultez [Référence des journaux Diagnostics Azure](/azure/azure-monitor/reference/tables/azurediagnostics).

1. Sélectionnez **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres pour cette fabrique de données. Les journaux de diagnostic sont diffusés en continu vers cet espace de travail dès que de nouvelles données d’événement sont générées. Jusqu’à 15 minutes peuvent s’écouler entre l’émission d’un événement et son affichage dans Log Analytics.

## <a name="next-steps"></a>Étapes suivantes

[Configurer les journaux de diagnostic via l’API REST Azure Monitor](monitor-logs-rest.md)
