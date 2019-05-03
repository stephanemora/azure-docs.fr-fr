---
title: 'Régression logistique multiclasse : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Multiclass Logistic Regression dans le service Azure Machine Learning pour créer un modèle de régression logistique qui peut être utilisé pour prédire plusieurs valeurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029324"
---
# <a name="multiclass-logistic-regression-module"></a>Module de régression logistique multiclasse

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle de régression logistique qui peut être utilisé pour prédire plusieurs valeurs.

Classification à l’aide de la régression logistique est une méthode d’apprentissage supervisé et nécessite par conséquent un jeu de données étiqueté. Vous formez le modèle en fournissant le modèle et le jeu de données étiqueté en tant qu’entrée à un module tel que [former le modèle](./train-model.md). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouveaux exemples d’entrée.

Azure Machine Learning fournit également un [Two-Class Logistic Regression](./two-class-logistic-regression.md) module, qui est adapté pour la classification des variables binaires ou dichotomiques.

## <a name="about-multiclass-logistic-regression"></a>À propos de la régression logistique multiclasse

La régression logistique est une méthode bien connue dans les statistiques qui sont utilisés pour prédire la probabilité d’un résultat et est souvent utilisée pour les tâches de classification. L’algorithme prédit la probabilité d’occurrence d’un événement en ajustant les données à une fonction logistique. 

Dans la régression logistique multiclasse, le classifieur peut être utilisé pour prédire plusieurs résultats.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurer une régression logistique multiclasse

1. Ajouter le **Multiclass Logistic Regression** module à l’expérience.

2. Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.

    + **L’unique paramètre**: Utilisez cette option si vous connaissez la façon dont vous souhaitez configurer le modèle et fournir un ensemble spécifique de valeurs comme arguments.

    + **Plage de paramètres**: Utilisez cette option si vous n’êtes pas sûr des meilleurs paramètres et que vous souhaitez utiliser un paramètre de balayage.

3. **Tolérance d’optimisation**, spécifiez la valeur de seuil de convergence de l’optimiseur. Si l’amélioration entre les itérations est inférieur au seuil, l’algorithme s’arrête et renvoie le modèle actuel.

4. **Poids de régularisation L1**, **poids de régularisation L2**: Tapez une valeur à utiliser pour les paramètres de régularisation L1 et L2. Une valeur non nulle est recommandée pour les deux.

    La régularisation est une méthode pour empêcher le surajustement en pénalisant les modèles avec des valeurs de coefficient extrêmes. La régularisation fonctionne en ajoutant la pénalité associée aux valeurs de coefficient à l’erreur de l’hypothèse. Un modèle précis avec des valeurs de coefficient extrêmes est pénalisé plus, mais un modèle moins précis avec des valeurs plus conservatrices est moins pénalisé.

     Régularisations L1 et L2 ont différents effets et utilise. L1 peut être appliqué aux modèles épars, ce qui est utile lorsque vous travaillez avec des données de grande dimension. En revanche, la régularisation L2 est préférable pour les données qui ne sont pas éparses.  Cet algorithme prend en charge une combinaison de valeurs de régularisation L1 et L2 : autrement dit, si `x = L1` et `y = L2`, `ax + by = c` définit le délai linéaire des termes du contrat de régularisation.

     Différentes combinaisons de linéaire des termes L1 et L2 ont été imaginés pour les modèles de régression logistique, tel que [élastique régularisation nette](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Valeur de départ de nombre aléatoire**: Tapez une valeur entière à utiliser comme valeur initiale pour l’algorithme si vous souhaitez que les résultats à chaque exécution. Sinon, une valeur de l’horloge système est utilisée comme valeur initiale, ce qui peut produire des résultats légèrement différents dans les exécutions de la même expérience.

8. Connectez un jeu de données étiquetée et l’un des modules de formation :

    + Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez le [former le modèle](./train-model.md) module.

9. Exécutez l’expérience.

## <a name="results"></a>Résultats

Une fois la formation terminée, vous pouvez voir un résumé des paramètres du modèle, ainsi que les poids de fonctionnalité tirées de formation, avec le bouton droit de la sortie de la [former le modèle](./train-model.md) module, puis sélectionnez **visualiser**.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 