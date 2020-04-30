---
title: Graphes de flux de données
description: Comment utiliser les graphiques de flux de données de fabrique de données
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605356"
---
# <a name="mapping-data-flow-graphs"></a>Graphiques de mappage des flux de données

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La surface de conception de mappage des flux de données est une surface de « construction » où vous créez des flux de données de haut en bas et de gauche à droite. Une boîte à outils est attachée à chaque transformation derrière le signe plus (+). Consacrez-vous à votre logique métier au lieu de connecter des nœuds par les bords dans un environnement DAG de forme libre.

Vous trouverez ci-dessous des mécanismes intégrés pour gérer le graphique de flux de données.

## <a name="move-nodes"></a>Déplacer des nœuds

![Options de transformation d’agrégation](media/data-flow/agghead.png "en-tête d’agrégateur")

Sans paradigme glisser-déplacer, la façon de déplacer un nœud de transformation consiste à changer le flux entrant. Au lieu de cela, vous allez déplacer les transformations en changeant le « flux entrant ».

## <a name="streams-of-data-inside-of-data-flow"></a>Flux de données à l’intérieur du flux de données

Dans Data Flow Azure Data Factory, les flux représentent le flux de données. Dans le volet des paramètres de transformation, le champ « Flux entrant » apparaît. Il vous indique quel flux de données entrant alimente cette transformation. Vous pouvez modifier l’emplacement physique de votre nœud de transformation sur le graphique en cliquant sur le nom du flux entrant et en sélectionnant un autre flux de données. La transformation active ainsi que toutes les transformations ultérieures sur ce flux seront ensuite déplacées vers le nouvel emplacement.

Si vous déplacez une transformation suivie d’une ou plusieurs transformations, le nouvel emplacement du flux de données sera joint par le biais d’une nouvelle branche.

S’il n’y a pas de transformation à la suite du nœud sélectionné, seule la transformation active sera déplacée vers le nouvel emplacement.

## <a name="hide-graph-and-show-graph"></a>Masquer le graphique et afficher le graphique

Il y a un bouton à l’extrême droite du volet de configuration inférieur dans lequel vous pouvez développer le volet inférieur en mode plein écran lorsque vous travaillez sur des configurations de transformation. Cela vous permet d’utiliser les boutons « précédent » et « suivant » pour parcourir les configurations du graphique. Pour revenir à la vue du graphique, cliquez sur le bouton inférieur et revenez à l’écran fractionné.

## <a name="search-graph"></a>Graphique de recherche

Vous pouvez effectuer une recherche dans le graphique à l’aide du bouton Rechercher sur l’aire de conception.

![action](media/data-flow/search001.png "Graphique de recherche")

## <a name="next-steps"></a>Étapes suivantes

Une fois la conception du flux de données terminée, activez le bouton de débogage et testez-le en mode débogage, directement dans le [concepteur de flux de données](concepts-data-flow-debug-mode.md) ou le [débogage de pipeline](control-flow-execute-data-flow-activity.md).
