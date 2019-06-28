---
title: 'Machine à vecteurs de support à deux classes : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module **Two-Class Support Vector Machine** (Machine à vecteurs de support à deux classes) dans Azure Machine Learning service pour créer un modèle basé sur l’algorithme de machine à vecteurs de support.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027929"
---
# <a name="two-class-support-vector-machine-module"></a>Module Two-Class Support Vector Machine (Machine à vecteurs de support à deux classes)

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour créer un modèle basé sur l’algorithme de machine à vecteurs de support. 

Les machines à vecteurs de support (SVM, support vector machines) représentent une classe bien étudiée de méthodes d’apprentissage supervisées. Cette implémentation particulière est adaptée à la prédiction de deux résultats possibles en fonction de variables continues ou catégorielles.

Après avoir défini les paramètres de modèle, effectuez l’apprentissage du modèle en utilisant les modules de formation et en fournissant un *jeu de données avec balises* qui inclut une colonne d’étiquettes ou de résultats.

## <a name="about-support-vector-machines"></a>À propos des machines à vecteurs de support

Les machines à vecteurs de support font partie des premiers algorithmes d’apprentissage automatique, et les modèles SVM ont été utilisés dans de nombreuses applications, de la récupération d’informations à la classification de texte et d’images. Les SVM peuvent être utilisées pour les tâches de classification et de régression.

Ce modèle de SVM est un modèle d’apprentissage supervisé qui requiert des données étiquetées. Dans le cadre du processus d’apprentissage, l’algorithme analyse les données d’entrée et reconnaît des modèles dans un espace de caractéristiques multidimensionnel appelé *hyperplan*.  Tous les exemples d’entrée sont représentés sous forme de points dans cet espace et sont mappés aux catégories de sortie de sorte que les catégories soient séparées par un écart aussi large et clair que possible.

Pour la prédiction, l’algorithme SVM attribue les nouveaux exemples dans une catégorie ou dans l’autre en les mappant au même espace. 

## <a name="how-to-configure"></a>Comment configurer 

Pour ce type de modèle, il est recommandé de normaliser le jeu de données avant de l’utiliser pour effectuer l’apprentissage du classifieur.
  
1.  Ajoutez le module **Two-Class Support Vector Machine** (Machine à vecteurs de support à deux classes) à votre expérience.  
  
2.  Spécifiez la méthode d’apprentissage que vous souhaitez appliquer au modèle en configurant l’option **Create trainer mode** (Créer un mode d’apprentissage).  
  
    -   **Single Parameter** (Paramètre unique) : si vous savez comment vous voulez configurer le modèle, vous pouvez fournir un ensemble spécifique de valeurs en tant qu’arguments.  

3.  Pour **Nombre d’itérations**, tapez un nombre indiquant le nombre d’itérations utilisées lors de la génération du modèle.  
  
     Ce paramètre peut être utilisé pour contrôler le compromis entre la précision et la vitesse de formation.  
  
4.  Pour **Lambda**, tapez une valeur à utiliser en tant que poids de la régularisation L1.  
  
     Ce coefficient de régularisation permet d’ajuster le modèle. Des valeurs plus élevées pénalisent les modèles plus complexes.  
  
5.  Sélectionnez l’option **Normalize features** (Normaliser les caractéristiques) si vous souhaitez normaliser les caractéristiques avant la formation.
  
     Si vous appliquez la normalisation, avant l’apprentissage, les points de données sont centrés sur la moyenne et mis à l’échelle de façon à présenter une unité d’écart type.
  
6.  Sélectionnez l’option **Project to the unit sphere** (Projeter sur la sphère d’unité) pour normaliser les coefficients.
  
     La projection des valeurs vers un espace d’unité signifie qu’avant l’apprentissage, les points de données sont centrés sur 0 et mis à l’échelle de façon à présenter une unité d’écart type.
  
7.  Sous **Random number seed** (Valeur de départ aléatoire), tapez une valeur entière à utiliser comme valeur de départ si vous souhaitez garantir la reproductibilité entre les exécutions.  Sinon, une valeur d’horloge système est utilisée comme valeur de départ, ce qui peut entraîner des résultats légèrement différents entre les exécutions.
  
9. Connectez un jeu de données étiqueté et l’un des [modules de formation](module-reference.md) :
  
    -   Si vous définissez **Create trainer mode** (Créer un mode d’apprentissage) sur **Single Parameter** (Paramètre unique), utilisez le module [Train Model](train-model.md) (Entraîner le modèle).
  

10. Exécutez l’expérience.

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle avec les pondérations de caractéristiques tirées de la formation, cliquez avec le bouton droit sur la sortie du module [Train Model](./train-model.md) (Entraîner le modèle), puis sélectionnez **Visualiser**.

+ Pour utiliser les modèles formés afin d’effectuer des prédictions, connectez le modèle formé au module [Score Model](score-model.md) (Noter le modèle).


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 