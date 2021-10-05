---
title: Transformation de fenêtre dans le flux de données de mappage
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez l’utilisation du flux de données de mappage Window Transformation dans les pipelines Azure Data Factory et Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: a913e7a7c5e3ce24df649cb0f1a670650992ac52
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059660"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Transformation de fenêtre dans le flux de données de mappage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

C’est lors de la transformation de fenêtres que vous allez définir des agrégations de colonnes utilisant une fenêtre dans vos flux de données. Dans le Générateur d’expressions, vous pouvez définir différents types d’agrégations basées sur les données ou sur des fenêtres de temps (clause SQL OVER), telles que LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Un nouveau champ qui inclut ces agrégations sera créé dans votre sortie. Vous pouvez également inclure des champs de regroupement facultatifs.

:::image type="content" source="media/data-flow/windows1.png" alt-text="Capture d’écran montrant l’option de fenêtrage sélectionnée dans le menu.":::

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Over
Définir le partitionnement des données de colonne pour la transformation de votre fenêtre. L’équivalent SQL est le ```Partition By``` dans la clause Over de SQL. Si vous souhaitez créer un calcul ou une expression à utiliser pour le partitionnement, vous pouvez le faire en plaçant le curseur sur le nom de colonne et en sélectionnant « colonne calculée ».

:::image type="content" source="media/data-flow/windows4.png" alt-text="Capture d’écran affichant la section des paramètres de fenêtrage avec l’onglet Over sélectionné.":::

## <a name="sort"></a>Trier
Une autre partie de la clause Over consiste à définir le ```Order By```. Ceci définit l’ordre de tri de données. Vous pouvez également créer une expression pour une valeur de calcul dans ce champ de colonne pour le tri.

:::image type="content" source="media/data-flow/windows5.png" alt-text="Capture d’écran affichant la section des paramètres de fenêtrage avec l’onglet Tri sélectionné.":::

## <a name="range-by"></a>Plage par
Ensuite, définir le cadre de fenêtre comme Non limité ou Limité. Pour définir un cadre de fenêtre non limité, positionnez le curseur sur Non limité aux deux extrémités. Si vous choisissez un paramètre entre Non limité et Ligne actuelle, vous devez définir les valeurs de décalage de début et de fin. Les deux valeurs seront des entiers positifs. Vous pouvez utiliser des nombres ou des valeurs relatifs à partir de vos données.

Le curseur de la fenêtre a deux valeurs à définir : les valeurs avant la ligne actuelle et les valeurs après la ligne actuelle. Le décalage de début et de fin correspond aux deux sélecteurs sur le curseur.

:::image type="content" source="media/data-flow/windows6.png" alt-text="Capture d’écran affichant la section des paramètres de fenêtrage avec l’onglet Plage par sélectionné.":::

## <a name="window-columns"></a>Colonnes de fenêtres
Enfin, utilisez le Générateur d’expressions pour définir les agrégations que vous souhaitez utiliser avec les fenêtres de données, telles que RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

:::image type="content" source="media/data-flow/windows7.png" alt-text="Capture d’écran présentant le résultat de l’action de fenêtrage.":::

La liste complète d’agrégation et de fonctions analytiques disponibles que vous pouvez utiliser dans le langage d’expression de flux de données via le Générateur d’expressions est répertoriée dans [Expressions de transformation de données dans le flux de données de mappage](data-flow-expression-functions.md).

## <a name="next-steps"></a>Étapes suivantes

Si vous recherchez une agrégation simple groupe par groupe, utilisez la [transformation Agrégat](data-flow-aggregate.md)
