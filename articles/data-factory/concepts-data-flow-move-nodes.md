---
title: Nœuds de déplacement de flux de données Azure Data Factory
description: Déplacement de nœuds dans un diagramme de flux de données Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 4328a090478747e9acbfb25cca45b330cd4a300b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212383"
---
#<a name="data-factory-data-flow-move-nodes"></a>Nœuds de déplacement de flux de données Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Options de transformation d’agrégation](media/data-flow/agghead.png "En-tête d’agrégation")

La surface de conception Data Flow Azure Data Factory est une surface de « construction » où vous créez des flux de données de haut en bas et de gauche à droite. Une boîte à outils est attachée à chaque transformation derrière le signe plus (+). Consacrez-vous à votre logique métier au lieu de connecter des nœuds par les bords dans un environnement DAG de forme libre.

Par conséquent, sans paradigme glisser-déplacer, la façon de déplacer un nœud de transformation consiste à changer le flux entrant. Au lieu de cela, vous allez déplacer les transformations en changeant le « flux entrant ».

Dans Data Flow Azure Data Factory, les flux représentent le flux de données. Dans le volet des paramètres de transformation, le champ « Flux entrant » apparaît. Il vous indique quel flux de données entrant alimente cette transformation. Vous pouvez modifier l’emplacement physique de votre nœud de transformation sur le graphique en cliquant sur le nom du flux entrant et en sélectionnant un autre flux de données. La transformation active ainsi que toutes les transformations ultérieures sur ce flux seront ensuite déplacées vers le nouvel emplacement.

Si vous déplacez une transformation suivie d’une ou plusieurs transformations, le nouvel emplacement du flux de données sera joint par le biais d’une nouvelle branche.

S’il n’y a pas de transformation à la suite du nœud sélectionné, seule la transformation active sera déplacée vers le nouvel emplacement.
