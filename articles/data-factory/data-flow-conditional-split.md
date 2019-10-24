---
title: Transformation du fractionnement conditionnel de flux de données de mappage pour Azure Data Factory
description: Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387905"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Mappage de la transformation du fractionnement conditionnel de Data Flow



![boîte à outils de fractionnement conditionnel](media/data-flow/conditionalsplit2.png "boîte à outils de fractionnement conditionnel")

La transformation du fractionnement conditionnel peut acheminer des lignes de données vers différents flux en fonction du contenu des données. L’implémentation de la transformation du fractionnement conditionnel est similaire à une structure de décision CASE dans un langage de programmation. La transformation évalue les expressions, et selon les résultats, dirige la ligne de données vers le flux spécifié. Cette transformation offre également une sortie par défaut. Ainsi, si une ligne ne correspond à aucune expression, elle est dirigée vers la sortie par défaut.

![fractionnement conditionnel](media/data-flow/conditionalsplit1.png "options de fractionnement conditionnel")

## <a name="multiple-paths"></a>Plusieurs chemins d’accès

Pour ajouter des conditions supplémentaires, sélectionnez « Ajouter un flux » dans le panneau de configuration du bas, puis cliquez dans la zone de texte Générateur d’expressions pour créer votre expression.

![fractionnement conditionnel multiple](media/data-flow/conditionalsplit3.png "fractionnement conditionnel multiple")

## <a name="next-steps"></a>Étapes suivantes

Transformations Data Flow courantes utilisées avec le fractionnement conditionnel : [transformation Joint](data-flow-join.md), [transformation Lookup](data-flow-lookup.md), [transformation Select](data-flow-select.md)
