---
title: 'Régression linéaire : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module de régression linéaire dans le service Azure Machine Learning pour créer un modèle de régression linéaire pour une utilisation dans une expérience.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029714"
---
# <a name="linear-regression-module"></a>Module de régression linéaire
Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de régression linéaire pour une utilisation dans une expérience.  Régression linéaire tente d’établir une relation linéaire entre une ou plusieurs variables indépendantes et un résultat numérique ou la variable dépendante. 

Vous utilisez ce module pour définir une méthode de régression linéaire et puis de former un modèle à l’aide d’un jeu de données étiqueté. Le modèle formé peut ensuite être utilisé pour effectuer des prédictions.

## <a name="about-linear-regression"></a>À propos de la régression linéaire

Régression linéaire est une méthode statistique courantes, ce qui a été adoptée dans machine learning et améliorée avec de nombreuses nouvelles méthodes pour le montage de la ligne de mesurer l’erreur. Dans le sens le plus simple, fait référence à la prédiction d’une cible numérique. Régression linéaire est toujours un bon choix lorsque vous souhaitez un modèle simple pour une tâche prédictive de base. Également, régression linéaire a tendance à fonctionner correctement sur les grands éparses jeux de données sans la complexité.

Azure Machine Learning prend en charge une variété de modèles de régression, en plus de régression linéaire. Toutefois, le terme « régression » peut être interprété faiblement et certains types de régression fourni dans d’autres outils ne sont pas pris en charge.

+ Le problème de régression classique implique une seule variable indépendante et une variable dépendante. Il s’agit *régression simple*.  Ce module prend en charge la régression simple.

+ *Régression linéaire multiple* implique deux ou plusieurs variables indépendantes qui contribuent à une seule variable dépendante. Problèmes dans lequel plusieurs entrées sont utilisées pour prédire un résultat numérique unique sont également appelés *régression linéaire MULTIVARIABLE*.

    Le **régression linéaire** module peut résoudre ces problèmes, comme la plupart des autres modules de régression.

+ *Régression à plusieurs étiquettes* est la tâche de prédiction de plusieurs variables dépendantes dans un modèle unique. Par exemple, dans la régression logistique à plusieurs étiquettes, un échantillon peut être attribué à plusieurs étiquettes différentes. (Cela est différent de la tâche de prédiction de plusieurs niveaux au sein d’une variable de classe unique).

    Ce type de régression n’est pas pris en charge dans Azure Machine Learning. Pour prédire plusieurs variables, créer un apprenant distinct pour chaque sortie que vous souhaitez prédire.

Pendant des années statisticiens ont développé des méthodes de régression de plus en plus avancées. Cela est vrai même pour la régression linéaire. Ce module prend en charge deux méthodes pour mesurer l’erreur et la régression : méthode des moindres carrés ordinaires et descente de gradient.

- **Descente de gradient** est une méthode qui réduit la quantité d’erreurs à chaque étape du processus de formation de modèle. Il existe de nombreuses variations de descente de gradient et son optimisation pour divers problèmes de formation a été largement étudiée. Si vous choisissez cette option pour **méthode Solution**, vous pouvez définir un grand nombre de paramètres pour contrôler la taille d’étape, taux d’apprentissage et ainsi de suite. Cette option prend également en charge l’utilisation d’un balayage de paramètre intégré.

- **Moindres carrés ordinaires** est une des techniques plus couramment utilisés dans une régression linéaire. Par exemple, les moindres carrés est la méthode qui est utilisée dans l’utilitaire d’analyse pour Microsoft Excel.

    Moindres carrés ordinaires fait référence à la fonction de perte, qui calcule l’erreur comme la somme du carré de la distance à partir de la valeur réelle et la ligne prédite et ajuste le modèle en minimisant l’erreur quadratique. Cette méthode suppose une forte relation linéaire entre les entrées et la variable dépendante.

## <a name="configure-linear-regression"></a>Configurer la régression linéaire

Ce module prend en charge deux méthodes pour l’ajustement d’un modèle de régression, avec différentes options :

+ [Créer un modèle de régression à l’aide de descente de gradient en ligne](#bkmk_GradientDescent)

    Descente de gradient est une meilleure fonction de perte pour les modèles qui sont plus complexes ou qui ont des données d’apprentissage trop peu étant données le nombre de variables.



+ [Ajuster un modèle de régression à l’aide des moindres carrés ordinaires](#bkmk_OrdinaryLeastSquares)

    Pour les petits jeux de données, il est préférable de sélectionner les moindres carrés ordinaires. Cela doit donner des résultats similaires à Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a> Créer un modèle de régression à l’aide des moindres carrés ordinaires

1. Ajouter le **modèle de régression linéaire** module à votre expérience dans l’interface.

    Vous trouverez ce module dans le **Machine Learning** catégorie. Développez **initialiser le modèle**, développez **régression**, puis faites glisser le **modèle de régression linéaire** module à votre expérience.

2. Dans le **propriétés** volet, dans le **méthode Solution** liste déroulante, sélectionnez **moindres carrés ordinaires**. Cette option spécifie la méthode de calcul utilisée pour rechercher la ligne de régression.

3. Dans **poids de régularisation L2**, tapez la valeur à utiliser en tant que le poids de régularisation L2. Nous vous recommandons d’utiliser une valeur différente de zéro pour éviter le surajustement.

     Pour en savoir plus sur les effets de la régularisation ajustement du modèle, consultez cet article : [Régularisations L1 et L2 pour l’apprentissage](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Sélectionnez l’option **terme intercept de Include**, si vous souhaitez afficher le terme pour l’interception.

    Désélectionnez cette option si vous n’avez pas besoin de consulter la formule de régression.

5. Pour **valeur initiale de nombre aléatoire**, vous pouvez éventuellement taper une valeur pour amorcer le Générateur de nombres aléatoires utilisé par le modèle.

    À l’aide d’une valeur de départ est utile si vous souhaitez conserver les mêmes résultats entre les différentes exécutions de la même expérience. Sinon, la valeur par défaut est d’utiliser une valeur de l’horloge système.


7. Ajouter le [former le modèle](./train-model.md) module à votre expérience, connectez un jeu de données étiqueté.

8. Exécutez l’expérience.

## <a name="results-for-ordinary-least-squares-model"></a>Résultats de modèle de moindres carrés ordinaires

Une fois la formation terminée :

+ Pour afficher les paramètres du modèle, avec le bouton droit de la sortie de formateur, puis sélectionnez **visualiser**.

+ Pour effectuer des prédictions, connectez le modèle formé à la [noter le modèle](./score-model.md) module, ainsi que d’un jeu de données de nouvelles valeurs. 


## <a name="bkmk_GradientDescent"></a> Créer un modèle de régression à l’aide de descente de gradient en ligne

1. Ajouter le **modèle de régression linéaire** module à votre expérience dans l’interface.

    Vous trouverez ce module dans le **Machine Learning** catégorie. Développez **initialiser le modèle**, développez **régression**et faites glisser le **modèle de régression linéaire** module à votre expérience

2. Dans le **propriétés** volet, dans le **méthode Solution** liste déroulante, choisissez **gradient en ligne** en tant que la méthode de calcul utilisée pour rechercher la ligne de régression.

3. Pour **créer un mode d’entraînement**, indiquer si vous souhaitez former le modèle avec un ensemble prédéfini de paramètres, ou si vous souhaitez optimiser le modèle à l’aide d’un balayage de paramètre.

    + **L’unique paramètre**: Si vous savez comment vous souhaitez configurer le réseau de régression linéaire, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.

   
4. Pour **taux d’apprentissage**, spécifiez le taux d’apprentissage initial pour l’optimiseur de descente de gradient stochastique.

5. Pour **nombre d’époques de formation**, une valeur qui indique le nombre de fois où l’algorithme doit effectuer une itération à travers des exemples de type. Pour les jeux de données avec un petit nombre d’exemples, ce nombre doit être suffisant volumineux pour atteindre la convergence.

6. **Normaliser les fonctionnalités**: Si vous avez déjà normalisées les données numériques utilisées pour former le modèle, vous pouvez désélectionner cette option. Par défaut, le module normalise toutes les entrées numériques à une plage comprise entre 0 et 1.

    > [!NOTE]
    > 
    > N’oubliez pas d’appliquer la même méthode de normalisation aux nouvelles données utilisées pour l’évaluation.

7. Dans **poids de régularisation L2**, tapez la valeur à utiliser en tant que le poids de régularisation L2. Nous vous recommandons d’utiliser une valeur différente de zéro pour éviter le surajustement.

    Pour en savoir plus sur les effets de la régularisation ajustement du modèle, consultez cet article : [Régularisations L1 et L2 pour l’apprentissage](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Sélectionnez l’option **taux d’apprentissage diminution**si vous voulez que le taux d’apprentissage à décroître la progression des itérations.  

10. Pour **valeur initiale de nombre aléatoire**, vous pouvez éventuellement taper une valeur pour amorcer le Générateur de nombres aléatoires utilisé par le modèle. À l’aide d’une valeur de départ est utile si vous souhaitez conserver les mêmes résultats entre les différentes exécutions de la même expérience.


12. Ajouter un jeu de données étiquetée et d’un des modules de formation.

    Si vous n’utilisez pas un balayage de paramètre, utilisez le [former le modèle](train-model.md) module.

13. Exécutez l’expérience.

## <a name="results-for-online-gradient-descent"></a>Résultats de descente de gradient en ligne

Une fois la formation terminée :

+ Pour effectuer des prédictions, connectez le modèle formé à la [noter le modèle](./score-model.md) module, ainsi que de nouvelles données d’entrée.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 