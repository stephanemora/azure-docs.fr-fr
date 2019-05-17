---
title: Nœuds de déplacement de flux de données Azure Data Factory
description: Comment déplacer des nœuds dans un Azure Data Factory mappage données diagramme de flux
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 951a5d4fcbd561b085b0377bde48e820dc8972a2
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519965"
---
# <a name="mapping-data-flow-move-nodes"></a>Nœuds de déplacement du mappage de Data Flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Options de transformation d’agrégation](media/data-flow/agghead.png "En-tête d’agrégation")

La surface de conception Data Flow Azure Data Factory est une surface de « construction » où vous créez des flux de données de haut en bas et de gauche à droite. Une boîte à outils est attachée à chaque transformation derrière le signe plus (+). Consacrez-vous à votre logique métier au lieu de connecter des nœuds par les bords dans un environnement DAG de forme libre.

Par conséquent, sans paradigme glisser-déplacer, la façon de déplacer un nœud de transformation consiste à changer le flux entrant. Au lieu de cela, vous allez déplacer les transformations en changeant le « flux entrant ».

## <a name="streams-of-data-inside-of-data-flow"></a>Flux de données à l’intérieur de flux de données

Dans Data Flow Azure Data Factory, les flux représentent le flux de données. Dans le volet des paramètres de transformation, le champ « Flux entrant » apparaît. Il vous indique quel flux de données entrant alimente cette transformation. Vous pouvez modifier l’emplacement physique de votre nœud de transformation sur le graphique en cliquant sur le nom du flux entrant et en sélectionnant un autre flux de données. La transformation active ainsi que toutes les transformations ultérieures sur ce flux seront ensuite déplacées vers le nouvel emplacement.

Si vous déplacez une transformation suivie d’une ou plusieurs transformations, le nouvel emplacement du flux de données sera joint par le biais d’une nouvelle branche.

S’il n’y a pas de transformation à la suite du nœud sélectionné, seule la transformation active sera déplacée vers le nouvel emplacement.

## <a name="next-steps"></a>Étapes suivantes

À l’issue de votre conception de flux de données, activer le bouton de débogage et le tester en mode débogage soit directement dans le [Concepteur de flux de données](concepts-data-flow-debug-mode.md) ou [débogage de pipeline](control-flow-execute-data-flow-activity.md).
