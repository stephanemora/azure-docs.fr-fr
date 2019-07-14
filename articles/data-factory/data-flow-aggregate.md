---
title: Transformation d’agrégation dans le flux de données de mappage - Azure Data Factory | Microsoft Docs
description: Découvrez comment agréger des données à grande échelle dans Azure Data Factory avec la transformation d’agrégation de flux de données de mappage.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312110"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Transformation d’agrégation dans le flux de données de mappage 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

C’est lors de la transformation d’agrégation que vous allez définir des agrégations de colonnes dans vos flux de données. À l’aide du Générateur d’expressions, vous pouvez définir différents types d’agrégations telles que SUM, MIN, MAX et COUNT qui peuvent être regroupées par colonnes calculées ou existantes.

## <a name="group-by"></a>Regrouper par
Sélectionnez une colonne existante ou créez une colonne calculée à utiliser en tant que groupe par la clause de votre agrégation. Pour utiliser une colonne existante, sélectionnez la colonne souhaitée dans la liste déroulante. Pour créer une colonne calculée, placez le curseur sur la clause et cliquez sur « Colonne calculée ». Cela a pour effet d’ouvrir le [Générateur d’expressions de Data Flow](concepts-data-flow-expression-builder.md). Une fois que vous avez créé votre colonne calculée, entrez le nom de colonne de sortie dans le champ Name as (Nommer sous). Si vous souhaitez ajouter un groupe supplémentaire par clause, placez le curseur sur une clause existante, puis cliquez sur « + ».

![Paramètres de regroupements des transformations d’agrégation](media/data-flow/agg.png "Paramètres de regroupements des transformations d’agrégation")

> [!NOTE]
> Un groupe par clause est facultatif dans une transformation d’agrégation.

## <a name="aggregate-column"></a>Colonne d’agrégation 
Choisissez l’onglet Agrégations pour créer des expressions d’agrégation. Vous pouvez choisir une colonne existante et remplacer la valeur par l’agrégation, ou créer un champ avec un nouveau nom. L’expression d’agrégation est entrée dans la zone de droite en regard du sélecteur de nom de colonne. Pour modifier l’expression, cliquez sur la zone de texte pour ouvrir le Générateur d’expressions. Pour ajouter une agrégation supplémentaire, pointez sur une expression existante, cliquez sur « + » pour créer une colonne d’agrégation ou un [modèle de colonne](concepts-data-flow-column-pattern.md).

![Paramètres d’agrégation de transformation d’agrégation](media/data-flow/agg2.png "Paramètres d’agrégation de transformation d’agrégation")

> [!NOTE]
> Chaque expression d’agrégation doit contenir au moins une fonction d’agrégation.

> [!NOTE]
> En mode Débogage, le Générateur d’expressions ne peut pas produire d’aperçus de données avec des fonctions d’agrégation. Pour afficher des aperçus de données pour les transformations d’agrégation, fermez le Générateur d’expressions et affichez les données via l’onglet Aperçu des données.

## <a name="next-steps"></a>Étapes suivantes

\- Définir l’agrégation en fonction d’une fenêtre à l’aide de la [transformation Fenêtre](data-flow-window.md)