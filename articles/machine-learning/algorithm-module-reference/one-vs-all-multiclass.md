---
title: One-vs-All Multiclass
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module One-vs-All Multiclass dans Azure Machine Learning service pour créer un modèle de classification multiclasse à partir d’un ensemble de modèles de classification binaire.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 9ded83dc5b8b8b98af66c8858214e8f82a4aa166
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511922"
---
# <a name="one-vs-all-multiclass"></a>One-vs-All Multiclass

Cet article explique comment utiliser le module **One-vs-All Multiclass** du concepteur Azure Machine Learning (préversion) pour créer un modèle de classification capable de prédire plusieurs classes selon l’approche « One-vs-All ».

Ce module est utile pour créer des modèles qui prédisent trois résultats possibles, lorsque le résultat dépend de variables de prédiction continues ou catégoriques. Cette méthode vous permet également d’utiliser des méthodes de classification binaire pour les problèmes qui requièrent plusieurs classes de sortie.

### <a name="more-about-one-vs-all-models"></a>En savoir plus sur les modèles One-vs-All

Bien que certains algorithmes de classification permettent d’utiliser plus de deux classes par conception, d’autres limitent les résultats possibles à l’une des deux valeurs (modèle binaire ou à deux classes). Toutefois, même les algorithmes de classification binaire peuvent être adaptés aux tâches de classification multiclasse à l’aide de différentes stratégies. 

Ce module implémente la *méthode One-vs-All*, dans laquelle un modèle binaire est créé pour chacune des classes de sortie multiples. Chacun de ces modèles binaires pour les classes individuelles est évalué par rapport à son complément (toutes les autres classes du modèle) comme s’il s’agissait d’un problème de classification binaire. La prédiction est ensuite effectuée en exécutant ces classifieurs binaires et en choisissant la prédiction avec le score de confiance le plus élevé.  

En résumé, un ensemble de modèles individuels est créé et les résultats sont ensuite fusionnés afin de créer un modèle unique qui prédit toutes les classes. Ainsi, tout classifieur binaire peut être utilisé comme base pour un modèle One-vs-All.  

 Par exemple, supposons que vous configurez un modèle de [Machine à vecteurs de support à deux classes](two-class-support-vector-machine.md) et que vous le fournissez en entrée dans le module **One-vs-All Multiclass**. Le module crée des modèles de machine à vecteurs de support à deux classes pour tous les membres de la classe de sortie, puis applique la méthode One-vs-All pour combiner les résultats de toutes les classes.  

## <a name="how-to-configure-the-one-vs-all-classifier"></a>Comment configurer le classifieur One-vs-All  

Ce module crée un ensemble de modèles de classification binaire pour analyser plusieurs classes. Par conséquent, pour utiliser ce module, vous devez tout d’abord configurer et entraîner un modèle de **classification binaire**. 

Vous connectez ensuite le modèle binaire au module **One-vs-All Multiclass** et vous entraînez l’ensemble des modèles à l’aide de [Entraîner le modèle](train-model.md) avec un jeu de données d’entraînement étiqueté.

Lorsque vous combinez les modèles, même si le jeu de données d’entraînement peut avoir plusieurs valeurs de classe, **One-vs-All Multiclass** crée plusieurs modèles de classification binaire, optimise l’algorithme pour chaque classe, puis fusionne les modèles.

1. Ajoutez **One-vs-All Multiclass** à votre pipeline dans le concepteur. Vous pouvez trouver ce module sous Machine Learning - Initialiser, dans la catégorie **Classification**.

    Le classifieur **One-vs-All Multiclass** ne comprend pas de paramètres configurables. Toutes les personnalisations doivent être effectuées dans le modèle de classification binaire fourni comme entrée.

2. Ajoutez un modèle de classification binaire au pipeline et configurez ce modèle. Par exemple, vous pouvez utiliser [Machine à vecteurs de support à deux classes](two-class-support-vector-machine.md) ou [Arbre de décision optimisé à deux classes](two-class-boosted-decision-tree.md).

3. Ajoutez le module [Entraîner le modèle](train-model.md) à votre pipeline, puis connectez le classifieur non entraîné qui correspond à la sortie de **One-vs-All Multiclass**.

4. Sur l’autre entrée de [Entraîner le modèle](train-model.md), connectez un jeu de données d’entraînement étiqueté qui comprend plusieurs valeurs de classe.

5. Exécuter le pipeline.

## <a name="results"></a>Résultats

Une fois l’entraînement terminé, vous pouvez utiliser le modèle pour effectuer des prédictions multiclasses.

Vous pouvez également transmettre le classifieur non entraîné à [Modèle de validation croisée](cross-validate-model.md) pour une validation croisée par rapport à un jeu de données de validation étiqueté.


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 
