---
title: Présentation des modèles
description: Découvrez comment utiliser un modèle prédéfini pour démarrer rapidement avec Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.author: susabat
author: ssabat
ms.custom: seo-lt-2019
ms.date: 09/09/2021
ms.openlocfilehash: c384985ae178f22c15c4a408d7a2cd42d0fbf416
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743393"
---
# <a name="templates"></a>Modèles

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Les modèles sont des pipelines Azure Data Factory prédéfinis qui permettent de démarrer rapidement avec Data Factory. Ils sont utiles aux débutants qui souhaitent se lancer sans délai. Ces modèles réduisent le temps de développement nécessaire à la création de projets d’intégration de données, améliorant ainsi la productivité des développeurs.

## <a name="create-data-factory-pipelines-from-templates"></a>Créer des pipelines Data Factory à partir de modèles

Vous pouvez commencer à créer un pipeline Data Factory à partir d’un modèle de deux façons :

1.  Sélectionnez **Modèles de pipeline** dans la section **Découvrir plus** de la page d’accueil de Data Factory pour ouvrir la galerie de modèles.

    :::image type="content" source="media/doc-common-process/home-page-pipeline-templates-tile.png" alt-text="Capture d’écran montrant comment ouvrir la galerie de modèles à partir de la page d'accueil Data Factory.":::

1.  Dans l’onglet Auteur de l’Explorateur de ressources, sélectionnez **+** , puis sélectionnez **Pipeline à partir du modèle** pour ouvrir la galerie de modèles.

    :::image type="content" source="media/solution-templates-introduction/templates-introduction-image-2.png" alt-text="Capture d’écran montrant comment ouvrir la galerie de modèles à partir de l’onglet Auteur.":::

## <a name="template-gallery"></a>Galerie de modèles

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-3.png" alt-text="Capture d’écran montrant la page Galerie de modèles.":::

### <a name="out-of-the-box-data-factory-templates"></a>Modèles Data Factory prêts à l’emploi

Data Factory utilise des modèles Azure Resource Manager pour enregistrer des modèles de pipelines Data Factory. Tous les modèles Resource Manager, ainsi que le fichier manifeste utilisé pour les modèles Data Factory prêts à l’emploi, se trouvent dans le [référentiel GitHub officiel d’Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Voici quelques exemples de modèles prédéfinis fournis par Microsoft :

-   Modèles de copie :

    -   [Copie en bloc à partir d’une base de données](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copier les nouveaux fichiers par LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copie de plusieurs conteneurs de fichiers entre différents magasins basés sur des fichiers](solution-template-copy-files-multiple-containers.md)

    -   [Déplacer des fichiers](solution-template-move-files.md)

    -   [Copie delta à partir d’une base de données](solution-template-delta-copy-with-control-table.md)

    -   Copie de \<source\> vers \<destination\>

        -   [D’Amazon S3 vers Azure Data Lake Storage Gen2](solution-template-migration-s3-azure.md)

        -   De Google BigQuery vers Azure Data Lake Storage Gen2

        -   De HDF vers Azure Data Lake Storage Gen2

        -   De Netezza vers Azure Data Lake Storage Gen1

        -   De SQL Server en local vers Azure SQL Database

        -   De SQL Server en local vers Azure Synapse Analytics

        -   D’Oracle en local vers Azure Synapse Analytics

-   Modèles SSIS

    -   Planifier l’exécution de packages SSIS par Azure-SSIS Integration Runtime

-   Modèles de transformation

    -   [ETL avec Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mes modèles

Vous pouvez également enregistrer un pipeline comme modèle en sélectionnant **Enregistrer comme modèle** dans l’onglet Pipeline.

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-4.png" alt-text="Capture d’écran montrant comment enregistrer un pipeline en tant que modèle.":::

Après avoir coché la case **Mes modèles** dans la page **Galerie de modèles**, vous pouvez voir les pipelines enregistrés en tant que modèle dans le volet droit de cette page. 

:::image type="content" source="media/solution-templates-introduction/templates-introduction-image-5.png" alt-text="Capture d’écran montrant le volet Mes modèles.":::

> [!NOTE]
> Pour pouvoir utiliser la fonctionnalité Mes modèles, vous devez activer l’intégration GIT. Azure DevOps GIT et GitHub sont pris en charge.
