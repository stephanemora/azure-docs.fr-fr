---
title: 'Régression de forêt d’arbres de décision : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Perceptron moyenné à deux classes dans Azure Machine Learning Service pour créer un modèle Machine Learning basé sur l’algorithme perceptron moyenné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 369974e52cb3585e4333b6033573b433e0a1a41a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693642"
---
# <a name="two-class-averaged-perceptron-module"></a>Module Perceptron moyenné à deux classes

Cet article décrit un module de l’interface visuelle (préversion) pour Azure Machine Learning Service.

Ce module vous permet de créer un modèle Machine Learning reposant sur l’algorithme perceptron moyenné.  
  
Cet algorithme de classification constitue une méthode d’apprentissage supervisée et nécessite un *jeu de données avec balises*, qui inclut une colonne d’étiquette. Vous pouvez effectuer l’apprentissage du modèle en fournissant un jeu de données avec balises et le modèle en tant qu’entrée pour [Train Model](./train-model.md) (Entraîner le modèle). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouveaux exemples d’entrées.  

### <a name="about-averaged-perceptron-models"></a>À propos des modèles Perceptron moyenné à deux classes

La *méthode Perceptron moyenné* est une version anticipée et simplifiée d’un réseau neuronal. Dans cette approche, les entrées sont classées en plusieurs sorties possibles selon une fonction linéaire, puis combinées avec un ensemble de poids dérivés du vecteur de fonctionnalité, d’où le nom « perceptron ».

Les modèles Perceptron les plus simples sont adaptés à l’apprentissage des modèles séparables de façon linéaire, tandis que les réseaux neuronaux (surtout les réseaux neuronaux profonds) peuvent modéliser des limites entre les classes plus complexes. Toutefois, les modules Perceptron sont plus rapides, et étant donné qu’ils traitent les cas en série, ils peuvent être utilisés avec une formation continue.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Comment configurer Perceptron moyenné à deux classes

1.  Ajoutez le module **Perceptron moyenné à deux classes** à votre pipeline.  

2.  Spécifiez le mode d’apprentissage du modèle en définissant l’option **Créer un mode d’apprentissage**.  
  
    -   **Single Parameter** (Paramètre unique) : si vous savez comment vous voulez configurer le modèle, fournissez un ensemble spécifique de valeurs en tant qu’arguments.
  
3.  Pour **Taux d’apprentissage**, spécifiez une valeur pour le *taux d’apprentissage*. Les valeurs de taux d’apprentissage contrôlent la taille de l’étape utilisée par SGD (Stochastic Gradient Descent) chaque fois que le modèle est testé et corrigé.
  
     En réduisant ce taux, vous testez le modèle plus souvent, avec le risque de rester bloqué dans un plateau local. En l’augmentant, vous pouvez converger plus rapidement, au risque de dépasser les minima réels.
  
4.  Pour **Nombre maximal d’itérations**, entrez le nombre de fois où vous voulez que l’algorithme examine les données de formation.  
  
     L’arrêt précoce offre une meilleure généralisation, la plupart du temps. L’augmentation du nombre d’itérations améliore l’ajustement, mais présente un risque de surajustement.
  
5.  Pour **Valeur de départ numérique aléatoire**, vous pouvez éventuellement saisir un entier comme valeur initiale. L’utilisation d’un seed est recommandée si vous souhaitez garantir la reproductibilité du pipeline au cours des exécutions.  
  
1.  Connecter un jeu de données d’apprentissage à l’un des modules de formation :
  
    -   Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), utilisez le module [Train Model](train-model.md) (Entraîner le modèle).

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle avec les pondérations de caractéristiques tirées de la formation, cliquez avec le bouton droit sur la sortie [Train Model](./train-model.md) (Entraîner le modèle).


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 