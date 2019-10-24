---
title: Transformation de recherche de flux de données de mappage Azure Data Factory
description: Transformation de recherche de flux de données de mappage Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 25d8588f8e2c968dc2516938263aaa7d6ddcff13
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387867"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformation de recherche de flux de données de mappage Azure Data Factory

Utilisez la recherche pour ajouter des données de référence d’une autre source à votre Data Flow. La transformation de recherche requiert une source définie qui pointe vers votre table de référence et qui correspond aux champs clés.

![Transformation de recherche](media/data-flow/lookup1.png "Recherche")

Sélectionnez les champs clés que vous souhaitez mettre en correspondance entre les champs de flux de données entrantes et les champs provenant de la source de référence. Vous devez d’abord créer une nouvelle source sur le canevas de conception du Data Flow à utiliser comme partie droite de la recherche.

Lorsque les correspondances sont trouvées, les lignes et les colonnes résultantes provenant de la source de référence seront ajoutés à votre flux de données. Vous pouvez choisir les champs d’intérêt que vous souhaitez inclure dans votre récepteur à la fin de votre Data Flow. Vous pouvez également utiliser une transformation de sélection à la suite de votre recherche pour élaguer la liste de champs afin de conserver uniquement les champs souhaités des deux flux.

La transformation de recherche effectue une action équivalente à celle d’une jointure externe gauche. Vous verrez donc toutes les lignes de la source de gauche se combiner aux lignes correspondantes du côté droit. Si votre recherche contient plusieurs valeurs correspondantes ou si vous souhaitez personnaliser l’expression de recherche, il est préférable d’utiliser une transformation de jointure avec une jointure croisée. Vous éviterez ainsi les erreurs de produit cartésien lors de l’exécution.

## <a name="match--no-match"></a>Correspondance / non-correspondance

Après votre transformation de recherche, vous pouvez utiliser d’autres transformations afin d’inspecter les résultats de chaque ligne de correspondance à l’aide de la fonction d’expression `isMatch()` pour effectuer des choix supplémentaires dans votre logique selon que la recherche a généré une correspondance de ligne ou non.

## <a name="optimizations"></a>Optimisations

Dans Data Factory, les flux de données s’exécutent dans des environnements à grande échelle Spark. Si votre jeu de données peut tenir dans l’espace mémoire de nœud Worder, nous pouvons optimiser les performances de vos recherches.

![Jonction de diffusion](media/data-flow/broadcast.png "Jonction de diffusion")

### <a name="broadcast-join"></a>Jonction de diffusion

Sélectionnez une jointure de diffusion côté gauche et/ou droit pour demander à ADF qu’il envoie en mémoire l’ensemble du jeu de données de chaque côté de la relation de recherche. Pour les jeux de données plus petits, ceci peut améliorer considérablement les performances de recherche.

### <a name="data-partitioning"></a>Partitionnement des données

Vous pouvez également spécifier le partitionnement de vos données en sélectionnant « Définir le partitionnement » sous l’onglet Optimisation de la transformation de recherche afin de créer des jeux de données qui s’adaptent mieux à la mémoire par Worker.

## <a name="next-steps"></a>Étapes suivantes

Les transformations [Join](data-flow-join.md) et [Exists](data-flow-exists.md) effectuent des tâches similaires dans les flux de données de mappage ADF. Examinons maintenant ces transformations.
