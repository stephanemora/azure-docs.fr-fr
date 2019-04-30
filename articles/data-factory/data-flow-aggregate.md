---
title: Transformation d’agrégation de mappage de Data Flow pour Azure Data Factory
description: Transformation d’agrégation de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467353"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Transformation d’agrégation de mappage de Data Flow pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

C’est lors de la transformation d’agrégation que vous allez définir des agrégations de colonnes dans vos flux de données. Dans le Générateur d’expressions, vous pouvez définir différents types d’agrégations (par ex., SUM, MIN, MAX, COUNT, etc.) et créer dans votre sortie un nouveau champ qui inclut ces agrégations avec des champs de regroupement facultatifs.

![Options de transformation d’agrégation](media/data-flow/agg.png "agrégat 1")

## <a name="group-by"></a>Regroupement
(Facultatif) Choisissez une clause de regroupement pour votre agrégation et utilisez le nom d’une colonne existante ou un nouveau nom. Utilisez « Ajouter une colonne » pour ajouter des clauses de regroupement et cliquez sur la zone de texte en regard du nom de la colonne pour lancer le Générateur d'expressions et sélectionner soit une colonne existante, soit une combinaison de colonnes ou d’expressions pour votre regroupement.

## <a name="the-aggregate-column-tab"></a>L’onglet Colonne d’agrégation 
(Obligatoire) Choisissez l’onglet Colonne d’agrégation pour créer les expressions d’agrégation. Vous pouvez choisir une colonne existante et remplacer la valeur par l’agrégation, ou créer un nouveau champ avec un nouveau nom pour l’agrégation. L’expression que vous souhaitez utiliser pour l’agrégation sera entrée dans la zone de droite en regard du sélecteur de nom de colonne. Cliquez sur cette zone de texte pour ouvrir le Générateur d’Expressions.

![Options de transformation d’agrégation](media/data-flow/agg2.png "agrégateur")

## <a name="data-preview-in-expression-builder"></a>Aperçu des données dans le Générateur d’expressions

En mode débogage, le Générateur d’expressions ne peut pas produire d’aperçus de données avec des fonctions d’agrégation. Pour afficher des aperçus de données pour les transformations d’agrégation, fermez le Générateur d’expressions et affichez le profil de données à partir du concepteur de flux de données.
