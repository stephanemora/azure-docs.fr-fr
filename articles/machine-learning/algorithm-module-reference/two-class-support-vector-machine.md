---
title: 'Machine à vecteurs de Support à deux classes : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le **Two-Class Support Vector Machine** module dans le service Azure Machine Learning pour créer un modèle qui est basé sur l’algorithme de machine de vecteur de prise en charge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027929"
---
# <a name="two-class-support-vector-machine-module"></a>Module de Two-Class Support Vector Machine

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle qui est basé sur l’algorithme de machine de vecteur de prise en charge. 

Machines à vecteurs de support (SVM) sont une classe bien connue des méthodes d’apprentissage supervisé. Cette implémentation particulière est adaptée à la prédiction des deux résultats possibles, en fonction des variables continues ou catégorielles.

Après avoir défini les paramètres de modèle, former le modèle en utilisant les modules de formation et en fournissant un *balisés dataset* qui inclut une colonne d’étiquette ou résultat.

## <a name="about-support-vector-machines"></a>Sur les machines à vecteurs de support

Machines à vecteurs de support sont parmi les premiers des algorithmes d’apprentissage automatique et des modèles SVM ont été utilisés dans de nombreuses applications, à partir de la récupération des informations à la classification de texte et image. SVM peuvent être utilisés pour les tâches de classification et de régression.

Ce modèle SVM est un modèle d’apprentissage supervisé qui requiert des données étiquetées. Dans le processus d’apprentissage, l’algorithme analyse les données d’entrée et reconnaît des motifs dans un espace multidimensionnel fonctionnalité appelée la *hyperplan*.  Tous les exemples d’entrée sont représentées sous forme de points dans cet espace et sont mappés aux catégories de sorte que les catégories sont divisées en largeur et effacer un écart possible de sortie.

Pour la prédiction, l’algorithme SVM affecte les nouveaux exemples dans une catégorie ou l’autre, en les mappant dans le même espace. 

## <a name="how-to-configure"></a>Comment configurer 

Pour ce type de modèle, il est recommandé de normaliser le jeu de données avant de l’utiliser pour former le classifieur.
  
1.  Ajouter le **Two-Class Support Vector Machine** module à votre expérience.  
  
2.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Si vous savez comment vous souhaitez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs comme arguments.  

3.  Pour **nombre d’itérations**, tapez un nombre qui indique le nombre d’itérations utilisées lors de la génération du modèle.  
  
     Ce paramètre peut être utilisé pour contrôler le compromis entre la vitesse de formation et de précision.  
  
4.  Pour **Lambda**, tapez une valeur à utiliser en tant que le poids de régularisation L1.  
  
     Ce coefficient de régularisation permet de paramétrer le modèle. Plus grandes valeurs pénalisent les modèles plus complexes.  
  
5.  Sélectionnez l’option **normaliser les fonctionnalités**, si vous souhaitez normaliser les fonctionnalités avant l’apprentissage.
  
     Si vous appliquez la normalisation, avant l’apprentissage, les points de données sont centrés à la moyenne et mis à l’échelle pour avoir une unité d’écart.
  
6.  Sélectionnez l’option **projet à la sphère d’unité**pour normaliser les coefficients.
  
     Projection de valeurs à l’espace de l’unité signifie que, avant l’apprentissage, des points de données sont centrées sur 0 et mis à l’échelle pour avoir une unité d’écart.
  
7.  Dans **valeur initiale de nombre aléatoire**, tapez une valeur entière à utiliser comme une valeur de départ si vous souhaitez garantir la reproductibilité entre les exécutions.  Sinon, une valeur de l’horloge système est utilisée comme valeur initiale, ce qui peut entraîner des résultats légèrement différents entre les exécutions.
  
9. Connecter un jeu de données étiquetée et l’autre de la [modules de formation](module-reference.md):
  
    -   Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez le [former le modèle](train-model.md) module.
  

10. Exécutez l’expérience.

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle, ainsi que les poids de fonctionnalité tirées de formation, cliquez sur la sortie de [former le modèle](./train-model.md), puis sélectionnez **visualiser**.

+ Pour utiliser les modèles formés pour élaborer des prédictions, connectez le modèle formé à la [noter le modèle](score-model.md) module.


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 