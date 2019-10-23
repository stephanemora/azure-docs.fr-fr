---
title: Nœuds de déplacement de flux de données Azure Data Factory
description: Déplacement de nœuds dans un diagramme de mappage de flux de données Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2031820843342fb7e6b115865297e08cbee28a0a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387332"
---
# <a name="mapping-data-flow-move-nodes"></a>Nœuds de déplacement du mappage de flux de données



![Options de transformation d’agrégation](media/data-flow/agghead.png "en-tête d’agrégateur")

La surface de conception Data Flow Azure Data Factory est une surface de « construction » où vous créez des flux de données de haut en bas et de gauche à droite. Une boîte à outils est attachée à chaque transformation derrière le signe plus (+). Consacrez-vous à votre logique métier au lieu de connecter des nœuds par les bords dans un environnement DAG de forme libre.

Par conséquent, sans paradigme glisser-déplacer, la façon de déplacer un nœud de transformation consiste à changer le flux entrant. Au lieu de cela, vous allez déplacer les transformations en changeant le « flux entrant ».

## <a name="streams-of-data-inside-of-data-flow"></a>Flux de données à l’intérieur du flux de données

Dans Data Flow Azure Data Factory, les flux représentent le flux de données. Dans le volet des paramètres de transformation, le champ « Flux entrant » apparaît. Il vous indique quel flux de données entrant alimente cette transformation. Vous pouvez modifier l’emplacement physique de votre nœud de transformation sur le graphique en cliquant sur le nom du flux entrant et en sélectionnant un autre flux de données. La transformation active ainsi que toutes les transformations ultérieures sur ce flux seront ensuite déplacées vers le nouvel emplacement.

Si vous déplacez une transformation suivie d’une ou plusieurs transformations, le nouvel emplacement du flux de données sera joint par le biais d’une nouvelle branche.

S’il n’y a pas de transformation à la suite du nœud sélectionné, seule la transformation active sera déplacée vers le nouvel emplacement.

## <a name="next-steps"></a>Étapes suivantes

Une fois la conception du flux de données terminée, activez le bouton de débogage et testez-le en mode débogage, directement dans le [concepteur de flux de données](concepts-data-flow-debug-mode.md) ou le [débogage de pipeline](control-flow-execute-data-flow-activity.md).
