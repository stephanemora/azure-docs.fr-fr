---
title: Transformation de fenêtres de flux de données de mappage
description: Azure Data Factory correspondant à la transformation de fenêtres de flux de données
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606221"
---
# <a name="azure-data-factory-window-transformation"></a>Transformation de la fenêtre Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

C’est lors de la transformation de fenêtres que vous allez définir des agrégations de colonnes utilisant une fenêtre dans vos flux de données. Dans le Générateur d’expressions, vous pouvez définir différents types d’agrégations basées sur les données ou sur des fenêtres de temps (clause SQL OVER), telles que LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Un nouveau champ qui inclut ces agrégations sera créé dans votre sortie. Vous pouvez également inclure des champs de regroupement facultatifs.

![Options de la fenêtre](media/data-flow/windows1.png "fenêtres 1")

## <a name="over"></a>Over
Définir le partitionnement des données de colonne pour la transformation de votre fenêtre. L’équivalent SQL est le ```Partition By``` dans la clause Over de SQL. Si vous souhaitez créer un calcul ou une expression à utiliser pour le partitionnement, vous pouvez le faire en plaçant le curseur sur le nom de colonne et en sélectionnant « colonne calculée ».

![Options de la fenêtre](media/data-flow/windows4.png "fenêtres 4")

## <a name="sort"></a>Trier
Une autre partie de la clause Over consiste à définir le ```Order By```. Ceci définit l’ordre de tri de données. Vous pouvez également créer une expression pour une valeur de calcul dans ce champ de colonne pour le tri.

![Options de la fenêtre](media/data-flow/windows5.png "fenêtres 5")

## <a name="range-by"></a>Plage par
Ensuite, définir le cadre de fenêtre comme Non limité ou Limité. Pour définir un cadre de fenêtre non limité, positionnez le curseur sur Non limité aux deux extrémités. Si vous choisissez un paramètre entre Non limité et Ligne actuelle, vous devez définir les valeurs de décalage de début et de fin. Les deux valeurs seront des entiers positifs. Vous pouvez utiliser des nombres ou des valeurs relatifs à partir de vos données.

Le curseur de la fenêtre a deux valeurs à définir : les valeurs avant la ligne actuelle et les valeurs après la ligne actuelle. Le décalage de début et de fin correspond aux deux sélecteurs sur le curseur.

![Options de la fenêtre](media/data-flow/windows6.png "fenêtres 6")

## <a name="window-columns"></a>Colonnes de fenêtres
Enfin, utilisez le Générateur d’expressions pour définir les agrégations que vous souhaitez utiliser avec les fenêtres de données, telles que RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

![Options de la fenêtre](media/data-flow/windows7.png "fenêtres 7")

La liste complète d’agrégation et de fonctions analytiques disponibles que vous pouvez utiliser dans le langage d’expression de flux de données ADF via le Générateur d’expressions est répertoriée ici : https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Étapes suivantes

Si vous recherchez une agrégation simple groupe par groupe, utilisez la [transformation Agrégat](data-flow-aggregate.md)
