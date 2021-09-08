---
title: Envoyer les données de traçabilité Data Factory à Azure Purview
description: En savoir plus sur l’envoi (push) de données de traçabilité Data Factory à Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 08/10/2021
ms.openlocfilehash: ff6c53efea27bc2bb4e6273180aa64178d6a0295
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424255"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Envoyer les données de traçabilité Data Factory à Azure Purview (préversion)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dans ce tutoriel, vous allez utiliser l’interface utilisateur Data Factory pour créer un pipeline qui exécute des activités et signale des données de traçabilité à un compte Azure Purview. Vous pouvez ensuite afficher toutes les informations de traçabilité dans votre compte Azure Purview. 

Actuellement, la traçabilité est prise en charge pour les activités Copy, Data Flow et Execute de SSIS. Pour plus d’informations sur les fonctionnalités prises en charge, consultez [Activités Azure Data Factory prises en charge](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="prerequisites"></a>Prérequis

* **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* **Azure Data Factory**. Si vous n’avez pas de fabrique de données Azure, consultez [Créer une fabrique de données Azure](./quickstart-create-data-factory-portal.md).
* **Compte Azure Purview**. Le compte Purview capture toutes les données de traçabilité générées par la fabrique de données. Si vous ne possédez pas de compte Azure Purview, consultez [Créer un compte Azure Purview](../purview/create-catalog-portal.md).

## <a name="run-pipeline-and-push-lineage-data-to-azure-purview"></a>Exécuter le pipeline et pousser (push) les données de traçabilité vers Azure Purview

### <a name="step-1-connect-data-factory-to-your-purview-account"></a>Étape 1 : Connecter Data Factory à votre compte Purview

Vous pouvez établir la connexion entre Data Factory et le compte Purview en suivant les étapes décrites dans [Connecter Data Factory à Azure Purview](connect-data-factory-to-azure-purview.md).

### <a name="step-2-run-pipeline-in-data-factory"></a>Étape 2 : Exécuter le pipeline dans Data Factory

Vous pouvez créer des pipelines, des activités Copy et des activités Dataflow dans Data Factory. Vous n’avez pas besoin de configuration supplémentaire pour la capture de données de traçabilité. Les données de traçabilité sont automatiquement capturées lors de l’exécution des activités.

:::image type="content" source="./media/data-factory-purview/adf-activities-for-lineage.png" alt-text="Capture d’écran des activités Copy et Dataflow.":::

:::image type="content" source="./media/data-factory-purview/ssis-activities-for-lineage.png" alt-text="Capture d’écran de l’activité Execute SSIS Package.":::

Découvrez-en plus sur la création d’activités Copy, Data Flow et Execute SSIS dans [Copier des données à partir d’un stockage Blob Azure vers une base de données dans Azure SQL Database en utilisant Azure Data Factory](./tutorial-copy-data-portal.md), [Transformer des données avec des flux de données de mappage](./tutorial-data-flow.md) et [Exécuter des packages SSIS dans Azure](./tutorial-deploy-ssis-packages-azure.md).

### <a name="step-3-monitor-lineage-reporting-status"></a>Étape 3 : Surveiller l’état des rapports de traçabilité

Après avoir exécuté le pipeline, dans la [vue de supervision du pipeline](monitor-visually.md#monitor-pipeline-runs), vous pouvez vérifier l’état du rapport de traçabilité en cliquant sur le bouton **État de la traçabilité** suivant. Les mêmes informations sont également disponibles dans la section `reportLineageToPurvew` de la sortie JSON de l’activité.

:::image type="content" source="./media/data-factory-purview/monitor-lineage-reporting-status.png" alt-text="Superviser l’état du rapport de traçabilité dans la vue de supervision du pipeline.":::

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Étape 4 : Afficher les informations de traçabilité dans votre compte Purview

Dans l’interface utilisateur de Purview, vous pouvez parcourir les ressources et choisir le type « Azure Data Factory ». Vous pouvez également effectuer une recherche dans Data Catalog en utilisant des mots clés.

:::image type="content" source="./media/data-factory-purview/view-dataset.png" alt-text="Capture d’écran de l’exploration des ressources dans Purview." lightbox="./media/data-factory-purview/view-dataset.png":::

Dans la ressource d’activité, cliquez sur l’onglet Traçabilité pour voir toutes les informations de traçabilité.

- Activité de copie :

    :::image type="content" source="./media/data-factory-purview/copy-lineage.png" alt-text="Capture d’écran de la traçabilité de l’activité Copy dans Purview." lightbox="./media/data-factory-purview/copy-lineage.png":::

- Activité Data Flow :

    :::image type="content" source="./media/data-factory-purview/dataflow-lineage.png" alt-text="Capture d’écran de la traçabilité de l’activité Data Flow dans Purview." lightbox="./media/data-factory-purview/dataflow-lineage.png":::

    > [!NOTE] 
    > En ce qui concerne la traçabilité de l’activité Dataflow, nous ne prenons en charge que la source et le récepteur. La traçabilité de la transformation Dataflow n’est pas encore prise en charge.

- Activité Execute SSIS Package :

    :::image type="content" source="./media/data-factory-purview/ssis-lineage.png" alt-text="Capture d’écran de la traçabilité de l’activité Execute SSIS dans Purview." lightbox="./media/data-factory-purview/ssis-lineage.png":::

    > [!NOTE] 
    > En ce qui concerne la traçabilité de l’activité Exécuter le package SSIS, nous ne prenons en charge que la source et la destination. La traçabilité de la transformation n’est pas encore prise en charge.

## <a name="next-steps"></a>Étapes suivantes

[Guide de l’utilisateur sur la traçabilité du catalogue](../purview/catalog-lineage-user-guide.md)

[Connecter Data Factory à Azure Purview](connect-data-factory-to-azure-purview.md)