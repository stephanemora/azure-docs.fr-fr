---
title: 'Les arbres de décision Tree Regression : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Boosted Decision Tree Regression dans le service Azure Machine Learning pour créer un ensemble d’arbres de régression à l’aide du boosting.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67e54f10074ee566ce974dbd27485904bfe0a653
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411543"
---
# <a name="boosted-decision-tree-regression-module"></a>Module de régression d’arbre de décision augmenté

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un ensemble d’arbres de régression à l’aide du boosting. *Boosting* signifie que chaque arbre dépend des arbres précédents. L’algorithme apprend en ajustant les arbres qui l’ont précédé la résiduels. Par conséquent, boosting dans un arbres de décision a tendance à améliorer la précision avec un léger risque de diminution de la couverture.  
  
Cette méthode de régression est une méthode d’apprentissage supervisé et nécessite donc un *étiqueté dataset*. La colonne d’étiquette doit contenir des valeurs numériques.  

> [!NOTE]
> Utilisez ce module uniquement avec les jeux de données qui utilisent des variables numériques.  

Une fois que vous avez défini le modèle, effectuez l’apprentissage à l’aide de la [former le modèle](./train-model.md).

> [!TIP]
> Vous voulez en savoir plus sur les arborescences qui ont été créés ? Une fois que le modèle a été formé, cliquez sur la sortie de la [former le modèle](./train-model.md) module et sélectionnez **visualiser** pour voir l’arborescence qui a été créé sur chaque itération. Vous pouvez explorer les fractionnements pour chaque arbre et afficher les règles pour chaque nœud.  
  
## <a name="more-about-boosted-regression-trees"></a>Plus d’informations sur les arbres de régression augmentés  

Le Boosting est une méthode classique de création de modèles d’ensemble, ainsi que le bagging, les forêts aléatoires et ainsi de suite.  Dans Azure Machine Learning, les arbres de décision augmentés utilisent une implémentation efficace de la mini-Data WAREHOUSE algorithme de gradient boosting. Gradient boosting est une technique pour les problèmes de régression d’apprentissage. Il génère chaque arbre de régression de manière par étapes, à l’aide d’une fonction de perte prédéfinie pour mesurer l’erreur dans chaque étape et de corriger à la suivante. Par conséquent, le modèle de prédiction est en fait un ensemble de modèles de prédiction plus faibles.  
  
Dans les problèmes de régression, boosting génère une série d’arbres par étapes, puis sélectionne l’arbre optimal à l’aide d’une fonction de perte dérivable arbitraire.  
  
Pour plus d’informations, consultez les articles suivants :  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Cet article de Wikipedia sur gradient boosting fournit des informations générales sur les arbres augmentés. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research : À partir de RankNet à LambdaRank à LambdaMART : Vue d’ensemble. Par J.C. Burges.

La méthode de gradient boosting peut également servir pour les problèmes de classification en les réduisant jusqu'à régression avec une fonction de perte appropriée. Pour plus d’informations sur l’implémentation des arbres augmentés pour les tâches de classification, consultez [Two-Class Boosted Decision Tree](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Comment configurer Boosted Decision Tree Regression

1.  Ajouter le **Boosted Decision Tree** module à votre expérience. Vous trouverez ce module sous **Machine Learning**, **initialiser**, sous le **régression** catégorie. 
  
2.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Sélectionnez cette option si vous connaissez la façon dont vous souhaitez configurer le modèle et fournir un ensemble spécifique de valeurs comme arguments.  
   
  
3. **Nombre maximal de feuilles par arbre**: Indiquer le nombre maximal de nœuds terminaux (feuilles) qui peuvent être créés dans n’importe quel arbre.  

    En augmentant cette valeur, vous potentiellement augmentez la taille de l’arborescence et obtenez une meilleure précision, au risque d’un surajustement et temps d’apprentissage.  

4. **Nombre minimal d’échantillons par nœud terminal**: Indiquer le nombre minimal de cas requis pour créer un nœud terminal (feuille) dans une arborescence.

    En augmentant cette valeur, vous augmentez le seuil pour la création de nouvelles règles. Par exemple, la valeur par défaut de 1, même un seul cas peut entraîner une nouvelle règle à créer. Si vous augmentez la valeur à 5, les données d’apprentissage doivent contenir au moins 5 cas respectant les mêmes conditions.

5. **Taux d’apprentissage**: Tapez un nombre compris entre 0 et 1 qui définit la taille d’étape lors de l’apprentissage. Le taux d’apprentissage détermine rapidement ou lentement l’apprenant converge vers la solution optimale. Si la taille d’étape est trop volumineux, vous pouvez dépassement de la solution optimale. Si la taille d’étape est trop petite, formation prend plus de temps à converger sur la meilleure solution.

6. **Nombre d’arbres construits**: Indique le nombre total d’arbres de décision à créer dans l’ensemble. En créant plusieurs arbres de décision, vous pouvez obtenir une meilleure couverture, mais augmente la durée d’apprentissage.

    Cette valeur contrôle également le nombre d’arbres affichés lorsque vous examinez le modèle formé. Si vous souhaitez afficher ou imprimer une arborescence unique, vous pouvez définir la valeur 1 ; Toutefois, qu’un seul arbre est généré (l’arborescence avec le jeu de paramètres initial) et aucune itération n’est effectuée.

7. **Valeur de départ de nombre aléatoire**: Tapez un entier non négatif facultatif à utiliser comme la valeur de départ aléatoire. En spécifiant une valeur initiale garantit la reproductibilité entre les exécutions qui ont les mêmes données et paramètres.

    Par défaut, la valeur de départ aléatoire est définie sur 0, ce qui signifie que la valeur de départ initiale est obtenue à partir de l’horloge système.
  
8. **Autoriser les niveaux catégoriels inconnus**: Sélectionnez cette option pour créer un groupe pour les valeurs inconnues dans les jeux d’apprentissage et de validation. Si vous désélectionnez cette option, le modèle peut accepter uniquement les valeurs qui sont contenus dans les données d’apprentissage. Le modèle peut être moins précis pour les valeurs connues, mais il peut fournir de meilleures prédictions pour les nouvelles valeurs (inconnues).

9. Ajoutez un jeu de données de formation et l’un des modules de formation :

    - Si vous définissez **créer un mode d’entraînement** option **seul paramètre**, utilisez le [former le modèle](train-model.md) module.  
  
    

10. Exécutez l’expérience.  
  
### <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher l’arborescence qui a été créé sur chaque itération, cliquez sur la sortie de la [former le modèle](train-model.md) module et sélectionnez **visualiser**.
  
     Cliquez sur chaque arbre pour Explorer les divisions et d’afficher les règles pour chaque nœud.  

+ Pour utiliser le modèle pour calculer les scores, connectez-le au [noter le modèle](./score-model.md), pour prédire des valeurs pour les nouveaux exemples d’entrée.

+ Pour enregistrer un instantané du modèle formé, cliquez sur le **ajouté pour l’apprentissage modèle** sortie du module de formation et sélectionnez **Enregistrer sous**. La copie du modèle formé que vous enregistrez n’est pas mis à jour lors des exécutions consécutives de l’expérience.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 