---
title: 'Régression d’arbre de décision boosté : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Régression d’arbre de décision boosté dans Azure Machine Learning pour créer un ensemble d’arbres de régression à l’aide du boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: fb3ff990dcc207fd6dff6073c7464b6e410431af
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76546875"
---
# <a name="boosted-decision-tree-regression-module"></a>Module de régression d’arbre de décision boosté

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Utilisez ce module pour créer un ensemble d’arbres de régression à l’aide du boosting. Le terme *boosting* signifie que chaque arbre dépend des arbres précédents. L’algorithme apprend en ajustant les valeurs résiduelles des arbres précédents. Par conséquent, le boosting d’un arbre de décision a tendance à améliorer la précision avec un léger risque de diminution de la couverture.  
  
Cette méthode de régression est une méthode d’apprentissage supervisé et nécessite donc un *jeu de données étiqueté*. La colonne de libellés doit contenir des valeurs numériques.  

> [!NOTE]
> Utilisez ce module uniquement avec les jeux de données qui contiennent des variables numériques.  

Une fois que vous avez défini le modèle, effectuez l’apprentissage à l’aide du module [Train model (Entraîner le modèle)](./train-model.md).

  
## <a name="more-about-boosted-regression-trees"></a>Plus d’informations sur les arbres de régression boostés  

Le boosting est une méthode classique de création de modèles d’ensemble, de même que le bagging, les forêts aléatoires, etc.  Dans Azure Machine Learning, les arbres de décision boostés utilisent une implémentation efficace de l’algorithme de boosting de gradient MART. Le boosting de gradient est une technique d’apprentissage automatique pour les problèmes de régression. Il génère chaque arbre de régression par étapes, à l’aide d’une fonction de perte prédéfinie pour mesurer l’erreur à chaque étape et la corriger à la suivante. Par conséquent, le modèle de prédiction est en fait un ensemble de modèles de prédiction plus faibles.  
  
Dans les problèmes de régression, le boosting génère une série d’arbres par étapes, puis sélectionne l’arbre optimal à l’aide d’une fonction de perte dérivable arbitraire.  
  
Pour plus d’informations, consultez les articles suivants :  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Cet article de Wikipedia sur le boosting de gradient fournit des informations générales sur les arbres boostés. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research : From RankNet to LambdaRank to LambdaMART: An Overview (De RankNet à LambdaRank et LambdaMART : présentation). De J.C. Burges.

La méthode de boosting de gradient peut également servir pour les problèmes de classification, en les réduisant jusqu’à régression avec une fonction de perte appropriée. Pour plus d’informations sur l’implémentation des arbres boostés pour les tâches de classification, voir [Arbre de décision boosté à deux classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Comment configurer une régression d’arbre de décision boosté

1.  Ajoutez le module **Arbre de décision optimisé** à votre pipeline. Vous le trouverez sous **Machine Learning**, **Initialiser**, dans la catégorie **Régression**. 
  
2.  Spécifiez le mode d’apprentissage du modèle en définissant l’option **Créer un mode d’apprentissage**.  
  
    -   **Single Parameter** (Paramètre unique) : sélectionnez cette option si vous savez comment vous voulez configurer le modèle, et fournissez un ensemble spécifique de valeurs en tant qu’arguments.  
   
  
3. **Maximum number of leaves per tree** (Nombre maximal de feuilles par arbre) : indiquer le nombre maximal de nœuds terminaux (feuilles) qui peuvent être créés dans n’importe quel arbre.  

    En augmentant cette valeur, vous augmentez potentiellement la taille de l’arbre et vous bénéficiez d’un surcroît de précision, au risque d’un surajustement et d’un temps d’apprentissage plus long.  

4. **Minimum number of samples per leaf node** (Nombre minimal d’échantillons par nœud terminal) : indiquez le nombre minimal de cas requis pour créer un nœud terminal (feuille) dans un arbre.

    Plus cette valeur est grande, plus le seuil de création de règles augmente. Par exemple, la valeur par défaut de 1, un seul cas suffit à entraîner la création d’une règle. Si vous définissez la valeur sur 5, les données d’apprentissage doivent contenir au moins 5 cas respectant les mêmes conditions.

5. **Taux d’apprentissage** : saisissez un nombre compris entre 0 et 1 qui définit la taille d’étape lors de l’apprentissage. Le taux d’apprentissage détermine la vitesse à laquelle l’apprentissage converge vers la solution optimale. Si la taille d’étape est trop grande, vous risquez de passer à côté de la solution optimale. Si la taille de pas est trop faible, l’apprentissage nécessite plus de temps pour converger vers la meilleure solution.

6. **Number of trees constructed** (Nombre d’arbres construits) : indiquez le nombre total d’arbres de décision à créer dans l’ensemble. Plus il y en a, meilleure peut être la couverture, mais plus le temps d’apprentissage augmente.

    Cette valeur contrôle également le nombre d’arbres qui s’affichent dans la visualisation du modèle entraîné. Si vous souhaitez afficher ou imprimer un arbre unique, vous pouvez définir la valeur sur 1. Toutefois, un seul arbre est produit (celui avec le jeu de paramètres initial) et aucune itération n’est effectuée.

7. **Random number seed** (Valeur de départ aléatoire) : saisissez un entier non négatif facultatif à utiliser comme la valeur de départ aléatoire. La spécification d’une valeur de départ garantit la reproductibilité entre les exécutions qui présentent les mêmes données et paramètres.

    Par défaut, la valeur de départ aléatoire est définie sur 0, ce qui signifie qu’elle est obtenue à partir de l’horloge système.
  
8. **Allow unknown categorical levels** (Autoriser les niveaux de catégorie inconnus) : sélectionnez cette option afin de créer un groupe pour les valeurs inconnues dans les jeux d’apprentissage et de validation. Si cette option est désélectionnée, le modèle n’accepte que les valeurs contenues dans les données d’apprentissage. Le modèle risque d’être moins précis pour les valeurs connues, mais il pourra fournir de meilleures prédictions pour les nouvelles valeurs (inconnues).

9. Ajoutez un jeu de données d’apprentissage et l’un des modules d’apprentissage :

    - Si vous définissez l’option **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), utilisez le module [Train Model](train-model.md) (Entraîner le modèle).  
  
    

10. Exécuter le pipeline.  
  
## <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour utiliser le modèle afin de calculer les scores, connectez-le au [modèle de score](./score-model.md), pour prédire des valeurs des nouveaux exemples d’entrée.

+ Pour enregistrer un instantané du modèle entraîné, sélectionnez l’onglet **Sorties** dans le panneau droit du **modèle entraîné** et cliquez sur l’icône **Inscrire le jeu de données**. La copie du modèle entraîné est enregistrée en tant que module dans l’arborescence du module et ne sera pas mise à jour lors des exécutions successives du pipeline.

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
