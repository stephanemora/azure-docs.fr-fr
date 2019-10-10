---
title: Azure Data Factory - Mappage d’une transformation Exists de flux de données
description: Comment rechercher des lignes existantes à l’aide du mappage de flux de données d’Azure Data Factory avec une transformation Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8b488a079b2da1bcf0dd064025ed251a1dc25213
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029393"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mappage d’une transformation Exists de flux de données



La transformation Exists est une transformation de filtrage de lignes qui stoppe ou autorise l'acheminement des lignes de vos données. La transformation Exists est similaire à ```SQL WHERE EXISTS``` et ```SQL WHERE NOT EXISTS```. Après une transformation Exists, les lignes obtenues à partir de votre flux de données incluent toutes les lignes où les valeurs de colonne de la source 1 existent ou non dans la source 2.

![Paramètres Exists](media/data-flow/exists.png "exists 1")

Sélectionnez la deuxième source pour votre Exists pour permettre au flux de données de comparer les valeurs du Flux 1 par rapport au Flux 2.

Sélectionnez la colonne de la Source 1 et de la Source 2 dont vous souhaitez vérifier les valeurs Exists ou Not Exists.

## <a name="multiple-exists-conditions"></a>Conditions Exists multiples

Lorsque vous pointez sur chaque ligne, un signe « + » apparaît à côté de chaque ligne, dans la colonne des conditions Exists. Il vous permet d’ajouter plusieurs lignes pour les conditions Exists. Chaque condition supplémentaire est un « Et ».

## <a name="custom-expression"></a>Expression personnalisée

![Paramètres Exists personnalisés](media/data-flow/exists1.png "Personnalisation Exists")

Vous pouvez cliquer sur « Expression personnalisée » pour créer une expression de forme libre en tant que condition Exists ou Not-exists. Le fait de cocher cette case vous permettra d’entrer votre propre expression en tant que condition.

## <a name="next-steps"></a>Étapes suivantes

Les transformations [Lookup](data-flow-lookup.md) et [Join](data-flow-join.md) sont similaires.
