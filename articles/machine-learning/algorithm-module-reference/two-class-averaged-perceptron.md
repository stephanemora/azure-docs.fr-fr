---
title: 'Régression de forêt de décision : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Two-Class Averaged Perceptron dans le service Azure Machine Learning pour créer un modèle machine learning basé sur l’algorithme perceptron Moyenné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029234"
---
# <a name="two-class-averaged-perceptron-module"></a>Module de Two-Class Averaged Perceptron

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un modèle machine learning basé sur l’algorithme perceptron Moyenné.  
  
Cet algorithme de classification est une méthode d’apprentissage supervisé et nécessite un *balisés dataset*, qui inclut une colonne d’étiquette. Vous pouvez former le modèle en fournissant le modèle et le jeu de données avec balises en tant qu’entrée [former le modèle](./train-model.md). Le modèle formé peut ensuite être utilisé pour prédire des valeurs pour les nouveaux exemples d’entrée.  

### <a name="about-averaged-perceptron-models"></a>Sur les modèles de perceptron Moyenné

Le *une moyenne de méthode de perceptron* est une version préliminaire et simple d’un réseau neuronal. Dans cette approche, les entrées sont classées en plusieurs sorties possibles selon une fonction linéaire et puis est combiné avec un ensemble de poids dérivés du vecteur de fonctionnalité, par conséquent, le nom « perceptron ».

Les modèles de perceptron les plus simples sont adaptés à l’apprentissage des modèles séparables de façon linéaire, tandis que les réseaux neuronaux (surtout les réseaux neuronaux profonds) peuvent modéliser des limites entre les classes plus complexes. Toutefois, les perceptrons sont plus rapides, et car ils traitent les cas en série, ils peuvent être utilisés avec un apprentissage continu.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Comment configurer Two-Class Averaged Perceptron

1.  Ajouter le **Two-Class Averaged Perceptron** module à votre expérience.  

2.  Spécifiez comment vous souhaitez que le modèle formé, en définissant le **créer un mode d’entraînement** option.  
  
    -   **L’unique paramètre**: Si vous savez comment vous souhaitez configurer le modèle, fournir un ensemble spécifique de valeurs comme arguments.
  
3.  Pour **taux d’apprentissage**, spécifiez une valeur pour le *taux d’apprentissage*. Le taux d’apprentissage valeurs contrôlent la taille de l’étape qui est utilisé dans la descente de gradient stochastique chaque fois que le modèle est testé et corrigé.
  
     En réduisant le taux, vous testez le modèle plus souvent, avec le risque que vous pouvez être bloqué dans un plateau local. En agrandissant le plus volumineux, vous pouvez converger plus rapidement, au risque de dépasser les vrais minima.
  
4.  Pour **nombre maximal d’itérations**, tapez le nombre de fois où vous voulez que l’algorithme pour examiner les données d’apprentissage.  
  
     Arrêt précoce offre souvent une meilleure généralisation. Augmentation du nombre d’itérations améliore l’ajustement, au risque de surajustement.
  
5.  Pour **valeur initiale de nombre aléatoire**, vous pouvez éventuellement taper une valeur entière à utiliser comme valeur initiale. L’utilisation d’une valeur initiale est recommandée si vous souhaitez garantir la reproductibilité de l’expérience sur s’exécute.  
  
1.  Connectez un jeu de données d’apprentissage et un des modules de formation :
  
    -   Si vous définissez **créer un mode d’entraînement** à **seul paramètre**, utilisez le [former le modèle](train-model.md) module.

## <a name="results"></a>Résultats

Une fois la formation terminée :

+ Pour afficher un résumé des paramètres du modèle, ainsi que les poids de fonctionnalité tirées de formation, cliquez sur la sortie de [former le modèle](./train-model.md).


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 