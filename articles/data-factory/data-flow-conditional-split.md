---
title: Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory
description: Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 9fe650465160ab837d8c8c191887d0f952976682
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271177"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Transformation du fractionnement conditionnel de Data Flow pour Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La transformation du fractionnement conditionnel peut acheminer des lignes de données vers différents flux en fonction du contenu des données. L’implémentation de la transformation du fractionnement conditionnel est similaire à une structure de décision CASE dans un langage de programmation. La transformation évalue les expressions, et selon les résultats, dirige la ligne de données vers le flux spécifié. Cette transformation offre également une sortie par défaut. Ainsi, si une ligne ne correspond à aucune expression, elle est dirigée vers la sortie par défaut.

![fractionnement conditionnel](media/data-flow/cd1.png "fractionnement conditionnel")

Pour ajouter des conditions supplémentaires, sélectionnez « Ajouter un flux » dans le panneau de configuration du bas, puis cliquez dans la zone de texte Générateur d’expressions pour créer votre expression.
