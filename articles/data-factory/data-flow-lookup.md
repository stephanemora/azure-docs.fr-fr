---
title: Transformation de recherche de mappage de Data Flow pour Azure Data Factory
description: Transformation de recherche de mappage de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029348"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformation de recherche de mappage de Data Flow pour Azure Data Factory



Utilisez la recherche pour ajouter des données de référence d’une autre source à votre Data Flow. La transformation de recherche requiert une source définie qui pointe vers votre table de référence et qui correspond aux champs clés.

![Transformation de recherche](media/data-flow/lookup1.png "Recherche")

Sélectionnez les champs clés que vous souhaitez mettre en correspondance entre les champs de flux de données entrantes et les champs provenant de la source de référence. Vous devez d’abord créer une nouvelle source sur le canevas de conception du Data Flow à utiliser comme partie droite de la recherche.

Lorsque les correspondances sont trouvées, les lignes et les colonnes résultantes provenant de la source de référence seront ajoutés à votre flux de données. Vous pouvez choisir les champs d’intérêt que vous souhaitez inclure dans votre récepteur à la fin de votre Data Flow.

## <a name="match--no-match"></a>Correspondance / non-correspondance

Après votre transformation de recherche, vous pouvez utiliser d’autres transformations afin d’inspecter les résultats de chaque ligne de correspondance à l’aide de la fonction d’expression `isMatch()` pour effectuer des choix supplémentaires dans votre logique selon que la recherche a généré une correspondance de ligne ou non.

## <a name="optimizations"></a>Optimisations

Dans Data Factory, les flux de données s’exécutent dans des environnements à grande échelle Spark. Si votre jeu de données peut tenir dans l’espace mémoire de nœud Worder, nous pouvons optimiser les performances de vos recherches.

![Jointure de diffusion](media/data-flow/broadcast.png "Jointure de diffusion")

### <a name="broadcast-join"></a>Jonction de diffusion

Sélectionnez une jointure de diffusion côté gauche et/ou droit pour demander à ADF qu’il envoie en mémoire l’ensemble du jeu de données de chaque côté de la relation de recherche.

### <a name="data-partitioning"></a>Partitionnement des données

Vous pouvez également spécifier le partitionnement de vos données en sélectionnant « Définir le partitionnement » sous l’onglet Optimisation de la transformation de recherche afin de créer des jeux de données qui s’adaptent mieux à la mémoire par Worker.

## <a name="next-steps"></a>Étapes suivantes

Les transformations [Joindre](data-flow-join.md) et [Existe](data-flow-exists.md) effectuent des tâches similaires dans le mappage des flux de données ADF. Examinons maintenant ces transformations.
