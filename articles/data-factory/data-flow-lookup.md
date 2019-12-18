---
title: Transformation de recherche du flux de données de mappage
description: Transformation de recherche de flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/03/2019
ms.openlocfilehash: 5cc54c95759ba1490f498305f05cc49a4411686d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930328"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformation de recherche de flux de données de mappage Azure Data Factory

Utilisez la recherche pour ajouter des données de référence d’une autre source à votre Data Flow. La transformation de recherche requiert une source définie qui pointe vers votre table de référence et qui correspond aux champs clés.

![Transformation de recherche](media/data-flow/lookup1.png "Recherche")

Sélectionnez les champs clés que vous souhaitez mettre en correspondance entre les champs de flux de données entrantes et les champs provenant de la source de référence. Vous devez d’abord créer une nouvelle source sur le canevas de conception du Data Flow à utiliser comme partie droite de la recherche.

Lorsque les correspondances sont trouvées, les lignes et les colonnes résultantes provenant de la source de référence seront ajoutés à votre flux de données. Vous pouvez choisir les champs d’intérêt que vous souhaitez inclure dans votre récepteur à la fin de votre Data Flow. Vous pouvez également utiliser une transformation de sélection à la suite de votre recherche pour élaguer la liste de champs afin de conserver uniquement les champs souhaités des deux flux.

La transformation de recherche effectue une action équivalente à celle d’une jointure externe gauche. Vous verrez donc toutes les lignes de la source de gauche se combiner aux lignes correspondantes du côté droit. Si votre recherche contient plusieurs valeurs correspondantes ou si vous souhaitez personnaliser l’expression de recherche, il est préférable d’utiliser une transformation de jointure avec une jointure croisée. Vous éviterez ainsi les erreurs de produit cartésien lors de l’exécution.

## <a name="match--no-match"></a>Correspondance / non-correspondance

Après votre transformation de recherche, vous pouvez utiliser d’autres transformations afin d’inspecter les résultats de chaque ligne de correspondance à l’aide de la fonction d’expression `isMatch()` pour effectuer des choix supplémentaires dans votre logique selon que la recherche a généré une correspondance de ligne ou non.

![Modèle de recherche](media/data-flow/lookup111.png "Modèle de recherche")

Après avoir utilisé la transformation de recherche, vous pouvez ajouter un fractionnement de la transformation de fractionnement conditionnel sur la fonction ```isMatch()```. Dans l’exemple ci-dessus, les lignes correspondantes passent par le flux principal, et les lignes sans correspondance transitent par le flux ```NoMatch```.

## <a name="first-or-last-value"></a>Première ou dernière valeur

Si vous obtenez plusieurs correspondances à partir de votre recherche, vous pouvez réduire les nombreuses lignes mises en correspondance par la sélection de la première ou de la dernière correspondance. Vous pouvez effectuer cette opération au moyen d’une transformation d’agrégation après votre recherche.

Dans ce cas, une transformation d’agrégation nommée ```PickFirst``` est utilisée pour choisir la première valeur à partir des correspondances de recherche.

![Agrégat de recherche](media/data-flow/lookup333.png "Agrégat de recherche")

![Rechercher en premier](media/data-flow/lookup444.png "Rechercher en premier")

## <a name="optimizations"></a>Optimisations

Dans Data Factory, les flux de données s’exécutent dans des environnements à grande échelle Spark. Si votre jeu de données peut tenir dans l’espace mémoire de nœud Worder, nous pouvons optimiser les performances de vos recherches.

![Jonction de diffusion](media/data-flow/broadcast.png "Jonction de diffusion")

### <a name="broadcast-join"></a>Jonction de diffusion

Sélectionnez une jointure de diffusion côté gauche et/ou droit pour demander à ADF qu’il envoie en mémoire l’ensemble du jeu de données de chaque côté de la relation de recherche. Pour les jeux de données plus petits, ceci peut améliorer considérablement les performances de recherche.

### <a name="data-partitioning"></a>Partitionnement des données

Vous pouvez également spécifier le partitionnement de vos données en sélectionnant « Définir le partitionnement » sous l’onglet Optimisation de la transformation de recherche afin de créer des jeux de données qui s’adaptent mieux à la mémoire par Worker.

## <a name="next-steps"></a>Étapes suivantes

* Les transformations [Join](data-flow-join.md) et [Exists](data-flow-exists.md) effectuent des tâches similaires dans les flux de données de mappage ADF. Examinons maintenant ces transformations.
* Utiliser un [Fractionnement conditionnel](data-flow-conditional-split.md) avec ```isMatch()``` pour fractionner des lignes sur des valeurs correspondantes et non correspondantes
