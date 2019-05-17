---
title: Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory
description: Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795623"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mappage de flux de données conditionnel transformation de fractionnement

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Conditional fractionner la boîte à outils](media/data-flow/conditionalsplit2.png "conditionnel fractionner la boîte à outils")

La transformation du fractionnement conditionnel peut acheminer des lignes de données vers différents flux en fonction du contenu des données. L’implémentation de la transformation du fractionnement conditionnel est similaire à une structure de décision CASE dans un langage de programmation. La transformation évalue les expressions, et selon les résultats, dirige la ligne de données vers le flux spécifié. Cette transformation offre également une sortie par défaut. Ainsi, si une ligne ne correspond à aucune expression, elle est dirigée vers la sortie par défaut.

![fractionnement conditionnel](media/data-flow/conditionalsplit1.png "options de fractionnement conditionnel")

## <a name="multiple-paths"></a>Plusieurs chemins d’accès

Pour ajouter des conditions supplémentaires, sélectionnez « Ajouter un flux » dans le panneau de configuration du bas, puis cliquez dans la zone de texte Générateur d’expressions pour créer votre expression.

![Conditional fractionner plusieurs](media/data-flow/conditionalsplit3.png "conditionnel fractionner multiples")

## <a name="next-steps"></a>Étapes suivantes

Transformations du flux de données courants utilisées avec fractionnement conditionnel : [Transformation de jointure](data-flow-join.md), [Loopup transformation](data-flow-lookup.md), [sélectionnez transformation](data-flow-select.md)
