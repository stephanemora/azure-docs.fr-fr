---
title: 'Régression logistique multiclasse : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Régression logistique multiclasse dans Azure Machine Learning service pour créer un modèle de régression logistique permettant de prédire plusieurs valeurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029324"
---
# <a name="multiclass-logistic-regression-module"></a>Module de régression logistique multiclasse

Cet article décrit un module de l’interface visuelle (en préversion) pour Azure Machine Learning service.

Utilisez ce module pour créer un modèle de régression logistique permettant de prédire plusieurs valeurs.

La classification à l’aide de la régression logistique est une méthode d’apprentissage supervisé qui nécessite donc un jeu de données étiqueté. Vous effectuez l’apprentissage du modèle en fournissant le modèle et le jeu de données étiqueté en tant qu’entrée pour un module tel que [Entraîner le modèle](./train-model.md). Le modèle entraîné est ensuite utilisable pour prédire les valeurs de nouveaux exemples d’entrées.

Azure Machine Learning fournit également un module [Régression logistique à deux classes](./two-class-logistic-regression.md), qui est adapté à la classification des variables binaires ou dichotomiques.

## <a name="about-multiclass-logistic-regression"></a>Présentation de la régression logistique multiclasse

La régression logistique est une méthode bien connue en statistiques qui sert à prédire la probabilité d’un résultat et qui est fréquemment utilisée pour les tâches de classification. L’algorithme prédit la probabilité d’occurrence d’un événement en ajustant les données à une fonction logistique. 

Dans une régression logistique multiclasse, le classifieur permet de prédire plusieurs résultats.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurer une régression logistique multiclasse

1. Ajoutez le module **Régression logistique multiclasse** à l’expérience.

2. Spécifiez le mode d’apprentissage du modèle en définissant l’option **Create trainer mode** (Créer un mode d’apprentissage).

    + **Paramètre unique** : sélectionnez cette option si vous savez comment vous voulez configurer le modèle, et fournissez un ensemble spécifique de valeurs en tant qu’arguments.

    + **Plage de paramètres** : utilisez cette option si vous n’êtes pas sûr des paramètres à choisir et que vous souhaitez utiliser un balayage paramétrique.

3. **Tolérance d’optimisation** : spécifiez la valeur de seuil de convergence de l’optimiseur. Si l’amélioration entre les itérations est inférieure à ce seuil, l’algorithme s’arrête et renvoie le modèle actuel.

4. **L1 regularization weight** (Pondération de régularisation L1), **L2 regularization weight** (L2 regularization weight) : saisissez une valeur à utiliser pour les paramètres de régularisation L1 et L2. La définition d’une valeur non nulle est recommandée pour les deux options.

    La régularisation est une méthode permettant d’empêcher le surajustement en pénalisant les modèles qui présentent des valeurs de coefficient extrêmes. La régularisation fonctionne en ajoutant la pénalité associée aux valeurs de coefficient à l’erreur de l’hypothèse. Un modèle précis avec des valeurs de coefficient extrêmes est pénalisé davantage, alors qu’un modèle moins précis avec des valeurs plus conservatrices est moins pénalisé.

     Les régularisations L1 et L2 ont différents effets et cas d’utilisation. La régularisation L1 est applicable aux modèles dispersés, ce qui est utile lorsque vous travaillez avec des données de grande dimension. En revanche, la régularisation L2 est préférable pour les données qui ne sont pas dispersées.  Cet algorithme prend en charge une combinaison linéaire de valeurs de régularisation L1 et L2 : autrement dit, si `x = L1` et `y = L2`, `ax + by = c` définit l’étendue linéaire des termes de régularisation.

     Différentes combinaisons linéaires de termes L1 et L2 ont été imaginées pour les modèles de régression logistique, comme la [régularisation de réseau élastique](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Random number seed** (Valeur de départ aléatoire) : saisissez une valeur entière à utiliser comme valeur initiale pour l’algorithme si vous souhaitez que les résultats puissent se répéter à chaque exécution. Sinon, une valeur d’horloge système est utilisée comme valeur initiale, ce qui peut générer des résultats légèrement différents dans les diverses exécutions d’une même expérience.

8. Connectez un jeu de données étiqueté, ainsi que l’un des modules d’apprentissage :

    + Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Paramètre unique**, utilisez le module [Train Model](./train-model.md) (Entraîner le modèle).

9. Exécutez l’expérience.

## <a name="results"></a>Résultats

Après l’apprentissage, vous pouvez visualiser un résumé des paramètres du modèle avec les pondérations de fonctionnalités tirées de l’apprentissage en cliquant avec le bouton droit sur la sortie du module [Train Model](./train-model.md) (Entraîner le modèle), puis en sélectionnant **Visualiser**.


## <a name="next-steps"></a>Étapes suivantes

Consultez les [modules disponibles](module-reference.md) pour Azure Machine Learning service. 