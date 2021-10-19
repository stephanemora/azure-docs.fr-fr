---
title: Créer un flux de données de mappage
description: Comment créer un mappage de flux de données Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/05/2021
ms.openlocfilehash: 1c47e42f3186d573fb57f1ebaa89140e0c713c0e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614499"
---
# <a name="create-azure-data-factory-data-flows"></a>Créer des flux de données Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Le mappage de flux de données permet de transformer des données à grande échelle sans aucun codage. Vous pouvez concevoir une tâche de transformation de données dans le concepteur de flux de données en créant une série de transformations. Commencez par un certain nombre de transformations sources, suivies par des étapes de transformation de données. Ensuite, réalisez votre flux de données avec un récepteur qui servira de destination à vos résultats.

## <a name="steps-to-create-a-new-data-flow"></a>Étapes de la création d’un flux de données

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory).

Commencez par [créer un Data Factory V2](quickstart-create-data-factory-portal.md) sur le portail Azure. Après avoir créé votre nouvelle fabrique, sélectionnez la vignette **Ouvrir Azure Data Factory Studio** dans le portail pour lancer Data Factory Studio.

:::image type="content" source="media/data-flow-create/open-data-factory-studio-from-portal.png" alt-text="Présente une capture d’écran de la procédure d’ouverture de Data Factory Studio à partir du portail Azure.":::

Vous pouvez ajouter des exemples de flux de données à partir de la galerie de modèles. Pour parcourir la galerie, sélectionnez l’onglet **Auteur** dans Data Factory Studio, puis cliquez sur le signe plus pour choisir **Pipeline** | **Galerie de modèles**.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-data-factory.png" alt-text="Illustre la procédure d’ouverture de la Galerie de modèles dans Data Factory.":::

Sélectionnez la catégorie Data Flow pour effectuer une sélection parmi les modèles disponibles.

:::image type="content" source="media/data-flow-create/template-gallery-filtered-for-data-flow.png" alt-text="Affiche la galerie de modèles filtrée sur les flux de données.":::

Vous pouvez également ajouter des flux de données directement à votre fabrique de données sans utiliser de modèle. Sélectionnez l’onglet **Auteur** dans Data Factory Studio, puis cliquez sur le signe plus pour choisir **Data Flow** | **Data Flow**.  

:::image type="content" source="media/data-flow-create/create-data-flow-directly.png" alt-text="Affiche une capture d’écran de la procédure de création directe d’un flux de données vide.":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

Commencez par [créer un espace de travail Synapse](../synapse-analytics/quickstart-create-workspace.md) à partir du portail Azure. Après avoir créé votre espace de travail, sélectionnez la vignette **Ouvrir Azure Studio** pour lancer l’interface utilisateur de Data Factory.
    
:::image type="content" source="media/data-flow-create/open-synapse-studio-from-portal.png" alt-text="Présente une capture d’écran de la procédure d’ouverture de Synapse Studio à partir du portail Azure.":::

Vous pouvez ajouter des exemples de flux de données à partir de la galerie de modèles.  Pour parcourir la galerie, sélectionnez l’onglet **Intégrer** dans Synapse Studio et cliquez sur le signe plus pour choisir **Parcourir la galerie**.

:::image type="content" source="media/data-flow-create/open-template-gallery-from-synapse.png" alt-text="Illustre la procédure d’ouverture de la galerie de modèles dans Data Factory.":::

Filtrez sur Catégorie : Data Flow pour effectuer une sélection parmi les modèles disponibles.

:::image type="content" source="media/data-flow-create/synapse-template-gallery-filtered-for-data-flow.png" alt-text="Affiche la galerie de modèles filtrée par flux de données.":::

Vous pouvez également ajouter des flux de données directement à votre espace de travail sans utiliser de modèle. Sélectionnez l’onglet **Intégrer** dans Synapse Studio et cliquez sur le signe plus pour choisir **Pipeline**.  Ensuite, dans votre pipeline, développez la section **Déplacer et transformer** les activités et faites glisser un **flux de données** vers le canevas pour le pipeline.

:::image type="content" source="media/data-flow-create/create-pipeline-in-synapse.png" alt-text="Affiche une capture d’écran de la procédure de création directe d’un pipeline vide.":::

:::image type="content" source="media/data-flow-create/add-data-flow-to-pipeline-synapse.png" alt-text="Affiche une capture d’écran de la procédure d’ajout direct d’un flux de données vide à un pipeline.":::

---

## <a name="next-steps"></a>Étapes suivantes

* [Didacticiel : Transformer des données avec des flux de données de mappage](tutorial-data-flow.md)
* Créez votre transformation des données avec une [transformation source](data-flow-source.md).
