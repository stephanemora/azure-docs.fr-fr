---
title: Guide pratique pour parcourir le catalogue de données
description: Cet article explique dans les grandes lignes comment parcourir le catalogue de données Azure Purview en fonction du type de ressource
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: ab5cb2856343d4fdcfcbd6c3c88d45889c191d2d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456810"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Explorer le catalogue de données Azure Purview

La recherche dans un catalogue de données est un outil formidable pour la découverte de données si un consommateur de données sait ce qu’il cherche, mais souvent les utilisateurs ne savent pas exactement comment leur patrimoine de données est structuré. Le catalogue de données Azure Purview offre une navigation qui permet aux utilisateurs d’explorer les données qui leur sont accessibles soit par collection, soit par le biais de la hiérarchie de chaque source de données dans le catalogue.

Pour accéder à l’expérience de navigation, sélectionnez « Parcourir les ressources » dans la page d’accueil du catalogue de données.

:::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Page d’accueil Purview" border="true":::

## <a name="browse-by-collection"></a>Parcourir par collection

L’exploration par collection vous permet d’explorer les différentes collections pour lesquelles vous êtes un lecteur de données ou un conservateur.

> [!NOTE]
> Vous ne voyez que les collections auxquelles vous avez accès. Pour plus d’informations, consultez [Créer et gérer des collections](how-to-create-and-manage-collections.md).

:::image type="content" source="media/how-to-browse-catalog/browse-by-collection.png" alt-text="Capture d’écran montrant la page Parcourir par collection" border="true":::

Une fois qu’une collection est sélectionnée, vous obtenez une liste des ressources de cette collection avec les facettes et les filtres disponibles dans la recherche. Comme une collection peut avoir des milliers d’éléments multimédias, la navigation utilise le moteur de pertinence de recherche Purview pour faire remonter les ressources les plus importantes en haut.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-results.png" alt-text="Capture d’écran montrant les résultats Parcourir par collection" border="true":::

Pour certaines annotations, vous pouvez cliquer sur les points pour choisir entre une condition ET ou une condition OU. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="Capture d’écran montrant comment choisir entre la condition ET ou OU" border="true":::

Si la collection sélectionnée ne contient pas les données que vous recherchez, vous pouvez facilement accéder aux collections associées ou revenir en arrière pour afficher l’ensemble de l’arborescence des collections.

:::image type="content" source="media/how-to-browse-catalog/browse-collection-navigation.png" alt-text="Capture d’écran montrant comment naviguer entre les collections" border="true":::

Une fois que vous avez trouvé l’élément multimédia que vous recherchez, vous pouvez le sélectionner pour afficher des détails supplémentaires tels que le schéma, la traçabilité et une liste de classification détaillée. Pour en savoir plus sur la page des détails de ressource, consultez [Gérer les ressources du catalogue](catalog-asset-details.md).

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Capture d’écran montrant la page de détails de la ressource" border="true":::

## <a name="browse-by-source-type"></a>Parcourir par type de source

L’exploration par type de source permet aux consommateurs de données d’explorer les hiérarchies de sources de données à l’aide d’une vue de l’Explorateur. Sélectionnez un type de source pour afficher la liste des sources analysées.

Il est par exemple facile de trouver un jeu de données nommé *DateDimension* dans un dossier nommé *Dimensions* dans Azure Data Lake Storage Gen2. Vous pouvez utiliser l’expérience « Parcourir par type de source » pour accéder au compte de stockage ADLS Gen2. Naviguez ensuite jusqu’au service > conteneur > dossier(s) pour atteindre le dossier *Dimensions* en question. La table *DateDimension* apparaît alors.

Une expérience de navigation native avec l’espace de noms hiérarchique est proposée pour chacune des sources de données correspondantes.

> [!NOTE]
> Après une analyse réussie, un certain délai peut être nécessaire avant que les nouvelles ressources analysées apparaissent dans l’expérience de navigation.


1. Sur la page **Parcourir par types de source**, les vignettes sont classées en fonction des sources de données. Pour approfondir l’exploration des ressources de chaque source de données, sélectionnez la vignette correspondante.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.png" alt-text="Page Parcourir les types de ressources" border="true":::

   > [!TIP]
   > Certaines vignettes sont des regroupements d’une collection de sources de données. Par exemple, la vignette Azure Storage Account contient tous les comptes Stockage Blob Azure et Azure Data Lake Storage Gen2. La vignette Azure SQL Server affiche les ressources Azure SQL Server qui contiennent Azure SQL Database et les instances de pool SQL dédiées Azure qui sont ingérées dans le catalogue. 

1. Sur la page suivante figurent les ressources de niveau supérieur sous le type de données choisi. Choisissez l’une d’elles pour explorer son contenu. Par exemple, après avoir sélectionné « Azure SQL Database », vous voyez une liste de bases de données contenant des ressources dans le catalogue de données.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.png" alt-text="Page d’exploration Azure SQL Database" border="true":::

1. Le mode Explorateur s’ouvre. Pour commencer, sélectionnez la ressource dans le volet gauche. Les ressources enfants s’affichent dans le volet droit de la page.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.png" alt-text="Mode Explorateur" border="true":::

1. Pour afficher les détails d’une ressource, sélectionnez le nom ou le bouton ellipse tout à droite.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.png" alt-text="Sélection du bouton ellipse pour afficher la page Détails de la ressource" border="true":::

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour créer, importer et exporter des termes de glossaire](how-to-create-import-export-glossary.md)
- [Guide pratique pour gérer les modèles de termes dans un glossaire métier](how-to-manage-term-templates.md)
- [Effectuer une recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
