---
title: Traçabilité à partir de SQL Server Integration Services
description: Cet article décrit l’extraction de la traçabilité des données à partir de SQL Server Integration Services.
author: chugugrace
ms.author: chugu
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: aa4fb6b7870831d0f24b0a7071cbf0dfd2431c14
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294556"
---
# <a name="how-to-get-lineage-from-sql-server-integration-services-ssis-into-azure-purview"></a>Comment obtenir la traçabilité de SQL Server Integration Services (SSIS) dans Azure Purview

Cet article aborde les aspects de la traçabilité des données de SQL Server Integration Services (SSIS) dans Azure Purview.

## <a name="prerequisites"></a>Configuration requise

- [Effectuer un « lift-and-shift » des charges de travail SQL Server Integration Services vers le cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

## <a name="supported-scenarios"></a>Scénarios pris en charge

L’étendue actuelle de la prise en charge comprend l’extraction de la traçabilité des packages SSIS exécutés par le SSIS Integration Runtime d’Azure Data Factory.

L’extraction de traçabilité SSIS locale n’est pas encore prise en charge.

### <a name="supported-data-stores"></a>Magasins de données pris en charge

| Banque de données | Prise en charge |
| ------------------- | ------------------- |
| Stockage Blob Azure | Oui |
| Azure Data Lake Storage Gen1 | Oui |
| Azure Data Lake Storage Gen2 | Oui |
| Stockage Fichier Azure | Oui |
| Azure SQL Database \* | Oui |
| Azure SQL Managed Instance\*| Oui |
| Azure Synapse Analytics\* | Oui |
| SQL Server \* | Oui |

*\* Azure Purview ne prend pas en charge à l’heure actuelle les requêtes ni les procédures stockées pour la traçabilité et l’analyse. La traçabilité est limitée aux sources de table et de vue uniquement.*


## <a name="how-to-bring-ssis-lineage-into-purview"></a>Comment intégrer la traçabilité SSIS dans Purview

### <a name="step-1-connect-a-data-factory-to-azure-purview"></a>Étape 1. [Connecter une fabrique de données à Azure Purview](how-to-link-azure-data-factory.md)

### <a name="step-2-trigger-ssis-activity-execution-in-azure-data-factory"></a>Étape 2. Déclencher l’exécution de l’activité SSIS dans Azure Data Factory

Vous pouvez [exécuter le package SSIS avec l’activité Exécuter le package SSIS](../data-factory/how-to-invoke-ssis-package-ssis-activity.md) ou [exécuter le package SSIS avec Transact-SQL dans le SSIS Integration Runtime d’ADF](../data-factory/how-to-invoke-ssis-package-stored-procedure-activity.md).  

Une fois l’activité Exécuter le package SSIS terminée, vous pouvez consulter l’état du rapport de traçabilité dans la sortie de l’activité sur le [moniteur d’activité Data Factory](../data-factory/monitor-visually.md#monitor-activity-runs).
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/activity-report-lineage-status.png" alt-text="ssis-status":::

### <a name="step-3-browse-lineage-information-in-your-azure-purview-account"></a>Étape 3. Parcourir les informations de traçabilité dans votre compte Azure Purview

- Vous pouvez parcourir le Data Catalog en choisissant le type de ressource « SQL Server Integration Services ».

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png" alt-text="Ressources du navigateur" lightbox="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-2.png" alt-text="browser-assets-ssis":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-3.png" alt-text="browse-assets-ssis-package":::

- Vous pouvez également effectuer une recherche dans Data Catalog en utilisant des mots clés.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/search-assets.png" alt-text="search-ssis" lightbox="media/how-to-lineage-sql-server-integration-services/search-assets.png":::

- Vous pouvez afficher les informations de traçabilité pour une activité SSIS Exécuter le package, et ouvrir celle-ci dans Data Factory pour afficher/modifier les paramètres d’activité.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage.png" alt-text="show-ssis-lineage" lightbox="media/how-to-lineage-sql-server-integration-services/lineage.png":::

- Vous pouvez choisir une source de données afin d’examiner la façon dont les colonnes dans la source sont mappées aux colonnes dans la destination.

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png" alt-text="show-ssis-lineage-drill-in" lightbox="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png":::

## <a name="next-steps"></a>Étapes suivantes

- [Effectuer un « lift-and-shift » des charges de travail SQL Server Integration Services vers le cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Découvrir la traçabilité des données dans Azure Purview](catalog-lineage-user-guide.md)
- [Lier Azure Data Factory pour pousser la traçabilité automatisée](how-to-link-azure-data-factory.md)