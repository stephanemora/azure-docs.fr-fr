---
title: Azure Data Factory mappage de flux de données dérivée de Transformation de colonne
description: Comment transformer des données à grande échelle avec Azure Data Factory mappage des données de flux de Transformation de colonne dérivée
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917578"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Mappage de flux de données dérivée de transformation de colonne

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la transformation de colonne dérivée pour générer de nouvelles colonnes dans votre flux de données ou pour modifier des champs existants.

![dériver une colonne](media/data-flow/dc1.png "Colonne dérivée")

Vous pouvez effectuer plusieurs actions de colonne dérivée dans une transformation de colonne dérivée unique. Cliquez sur « Ajouter une colonne » pour transformer plusieurs colonnes lors de l’étape de transformation unique.

Dans le champ de colonne, sélectionnez une colonne existante à remplacer par une nouvelle valeur dérivée, ou cliquez sur « Créer une colonne » pour générer une nouvelle colonne avec la valeur qui vient d’être dérivée.

La zone de texte Expression ouvre le Générateur d’expressions où vous pouvez créer l’expression pour les colonnes dérivées, à l’aide des fonctions d’expression.

## <a name="column-patterns"></a>Modèles de colonne

Si vos noms de colonnes sont des variables à partir de vos sources, vous pouvez souhaiter créer des transformations à l’intérieur de la colonne dérivée à l’aide du modèle de colonne au lieu d’utiliser les colonnes nommées. Consultez le [une dérive du schéma](concepts-data-flow-schema-drift.md) article pour plus d’informations.

![modèle de colonne](media/data-flow/columnpattern.png "modèle de colonne")

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [langage d’expression de fabrique de données pour les transformations](https://aka.ms/dataflowexpressions) et [Générateur d’expressions](concepts-data-flow-expression-builder.md)
