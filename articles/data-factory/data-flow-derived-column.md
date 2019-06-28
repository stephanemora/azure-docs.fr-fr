---
title: Transformation de colonne dérivée du flux de données de mappage d’Azure Data Factory
description: Comment transformer des données à l’échelle avec la transformation de colonne dérivée du flux de données de mappage d’Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917578"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Transformation de colonne dérivée du flux de données de mappage

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Utilisez la transformation de colonne dérivée pour générer de nouvelles colonnes dans votre flux de données ou pour modifier des champs existants.

![dériver une colonne](media/data-flow/dc1.png "Colonne dérivée")

Vous pouvez effectuer plusieurs actions de colonne dérivée dans une transformation de colonne dérivée unique. Cliquez sur « Ajouter une colonne » pour transformer plusieurs colonnes lors de l’étape de transformation unique.

Dans le champ de colonne, sélectionnez une colonne existante à remplacer par une nouvelle valeur dérivée, ou cliquez sur « Créer une colonne » pour générer une nouvelle colonne avec la valeur qui vient d’être dérivée.

La zone de texte Expression ouvre le Générateur d’expressions où vous pouvez créer l’expression pour les colonnes dérivées, à l’aide des fonctions d’expression.

## <a name="column-patterns"></a>Modèles de colonne

Si vos noms de colonnes varient en fonction de vos sources, vous souhaiterez peut-être créer des transformations à l’intérieur de la colonne dérivée à l’aide de modèles de colonne au lieu d’utiliser des colonnes nommées. Consultez l’article [Dérive de schéma](concepts-data-flow-schema-drift.md) pour plus d’informations.

![modèle de colonne](media/data-flow/columnpattern.png "Modèles de colonne")

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [langage d’expression de Data Factory pour les transformations](https://aka.ms/dataflowexpressions) et le [Générateur d’expressions](concepts-data-flow-expression-builder.md)
