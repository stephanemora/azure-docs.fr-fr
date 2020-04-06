---
title: 'Transformation de sélection de colonnes : sur le module Modifier les métadonnées'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Transformation de sélection de colonnes dans Azure Machine Learning pour créer une transformation qui sélectionne le même sous-ensemble de colonnes que dans le jeu de données concerné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455908"
---
# <a name="select-columns-transform"></a>Transformation de sélection de colonnes

Cet article décrit comment utiliser le module Transformation de sélection de colonnes dans le concepteur Azure Machine Learning (préversion). L’objectif du module Transformation de sélection de colonnes consiste à s’assurer qu’un ensemble de colonnes prévisible et cohérent est utilisé dans les opérations de machine learning en aval.

Ce module est utile pour des tâches telles que le scoring, qui nécessitent des colonnes spécifiques. Les modifications apportées aux colonnes disponibles peuvent rompre le pipeline ou changer les résultats.

Vous utilisez le module Transformation de sélection de colonnes pour créer et enregistrer un ensemble de colonnes. Ensuite, vous utilisez le module [Appliquer une transformation](apply-transformation.md) pour appliquer ces sélections aux nouvelles données.

## <a name="how-to-use-select-columns-transform"></a>Utilisation du module Transformation de sélection de colonnes

Ce scénario suppose que vous souhaitez utiliser la sélection de caractéristiques pour générer un ensemble dynamique de colonnes qui seront utilisées afin d’entraîner un modèle. Pour vous assurer que les sélections de colonnes soient identiques pour le processus de scoring, utilisez le module Transformation de sélection de colonnes afin de capturer les sélections de colonnes et de les appliquer ailleurs dans le pipeline.

1. Ajoutez un jeu de données d’entrée à votre pipeline dans le concepteur.

2. Ajoutez une instance du module [Sélection de caractéristiques par filtrage](filter-based-feature-selection.md).

3. Connectez les modules et configurez le module de sélection de caractéristiques pour rechercher automatiquement un certain nombre de caractéristiques optimales dans le jeu de données d’entrée.

4. Ajoutez une instance du module [Entraîner un modèle](train-model.md) et utilisez la sortie du module [Sélection de caractéristiques par filtrage](filter-based-feature-selection.md) comme entrée pour l’entraînement.

    > [!IMPORTANT]
    > Étant donné que l’importance d’une caractéristique est basée sur les valeurs de la colonne, vous ne pouvez pas connaître à l’avance les colonnes éventuellement disponibles en entrée pour le module [Entraîner un modèle](train-model.md).  
5. Attachez une instance du module Transformation de sélection de colonnes. 

    Cette étape génère une sélection de colonnes sous la forme d’une transformation qui peut être enregistrée ou appliquée à d’autres jeux de données. Cette étape garantit que les colonnes identifiées dans la sélection des caractéristiques sont enregistrées en vue de leur réutilisation par d’autres modules.

6. Ajoutez le module [Noter le modèle](score-model.md). 

   *Ne connectez pas le jeu de données d’entrée.* Au lieu de cela, ajoutez le module [Appliquer une transformation](apply-transformation.md) et connectez la sortie de la transformation de sélection de caractéristiques.

   > [!IMPORTANT]
   > Vous ne pouvez pas escompter les mêmes résultats si vous appliquez le module [Sélection de caractéristiques par filtrage](filter-based-feature-selection.md) au jeu de données de scoring. Comme la sélection des caractéristiques est basée sur des valeurs, elle peut choisir un autre ensemble de colonnes, ce qui entraînerait l’échec de l’opération de scoring.
7. Envoyez le pipeline.

Ce processus d’enregistrement, puis d’application d’une sélection de colonnes garantit que le même schéma de données est disponible pour l’entraînement et le scoring.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
