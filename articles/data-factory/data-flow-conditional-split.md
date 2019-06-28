---
title: Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory
description: Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795623"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mappage de la transformation du fractionnement conditionnel de Data Flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![boîte à outils du fractionnement conditionnel](media/data-flow/conditionalsplit2.png "boîte à outils du fractionnement conditionnel")

La transformation du fractionnement conditionnel peut acheminer des lignes de données vers différents flux en fonction du contenu des données. L’implémentation de la transformation du fractionnement conditionnel est similaire à une structure de décision CASE dans un langage de programmation. La transformation évalue les expressions, et selon les résultats, dirige la ligne de données vers le flux spécifié. Cette transformation offre également une sortie par défaut. Ainsi, si une ligne ne correspond à aucune expression, elle est dirigée vers la sortie par défaut.

![fractionnement conditionnel](media/data-flow/conditionalsplit1.png "options de fractionnement conditionnel")

## <a name="multiple-paths"></a>Plusieurs chemins d’accès

Pour ajouter des conditions supplémentaires, sélectionnez « Ajouter un flux » dans le panneau de configuration du bas, puis cliquez dans la zone de texte Générateur d’expressions pour créer votre expression.

![multiples fractionnements conditionnels](media/data-flow/conditionalsplit3.png "multiples fractionnements conditionnels")

## <a name="next-steps"></a>Étapes suivantes

Transformations Data Flow courantes utilisées avec le fractionnement conditionnel : [transformation Joint](data-flow-join.md), [transformation Loopup](data-flow-lookup.md), [transformation Select](data-flow-select.md)
