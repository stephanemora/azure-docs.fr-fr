---
title: 'Évaluer le modèle : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Évaluer le modèle dans Azure Machine Learning pour mesurer la justesse d’un modèle entraîné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 192aae4da4d9b07804f473ec6e98615f46e2ee86
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214584"
---
# <a name="evaluate-model-module"></a>Module Évaluer le modèle

Cet article décrit un module dans le concepteur Azure Machine Learning (préversion).

Utilisez ce module pour mesurer la précision d’un modèle formé. Vous fournissez un jeu de données qui contient les scores générés à partir d’un modèle et le module **Évaluer le modèle** calcule un ensemble de métriques d’évaluation standard du secteur.
  
 Les métriques retournées par **Évaluer le modèle** varient selon le type de modèle que vous évaluez :  
  
-   **Modèles de classification**    
-   **Modèles de régression**    


> [!TIP]
> Si vous ne connaissez pas l’évaluation du modèle, nous vous recommandons la série de vidéos du Dr. Stephen Elston, dans le cadre du [cours de Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) à partir d’EdX. 


Trois méthodes d’utilisation du module **Évaluer le modèle** sont possibles :

+ Générer des scores sur vos données d’apprentissage et évaluer le modèle en fonction de ces scores
+ Générer des scores sur le modèle, mais comparer ces scores à ceux d’un jeu de tests réservé
+ Comparer les scores de deux modèles différents mais liés, en utilisant le même jeu de données

## <a name="use-the-training-data"></a>Utiliser les données d’apprentissage

Pour évaluer un modèle, vous devez connecter un jeu de données qui contient un ensemble de colonnes d’entrée et de scores.  Si aucune autre donnée n’est disponible, vous pouvez utiliser votre jeu de données d’origine.

1. Connectez la sortie du **jeu de données noté** du module [Scorer le modèle](./score-model.md) à l’entrée du module **Évaluer le modèle**. 
2. Cliquez sur le module **Évaluer le modèle**, puis exécutez le pipeline pour générer les scores d’évaluation.

## <a name="use-testing-data"></a>Utiliser des données de test

Un scénario courant de Machine Learning consiste à séparer votre jeu de données d’origine en jeux de données d’apprentissage et de test, à l’aide du module [Fractionner](./split-data.md) ou du module [Partition et échantillon](./partition-and-sample.md). 

1. Connectez la sortie du **jeu de données noté** du module [Scorer le modèle](score-model.md) à l’entrée du module **Évaluer le modèle**. 
2. Connectez la sortie du module Fractionner des données qui contient les données de test à l’entrée de droite du module **Évaluer le modèle**.
2. Cliquez sur le module **Évaluer le modèle**, puis sélectionnez **Exécuter la sélection** pour générer les scores d’évaluation.

## <a name="compare-scores-from-two-models"></a>Comparer les scores de deux modèles

Vous pouvez également connecter un second jeu de résultats au module **Évaluer le modèle**.  Les scores peuvent être un ensemble d’évaluations partagé contenant des résultats connus, ou un ensemble de résultats d’un autre modèle pour les mêmes données.

Cette fonctionnalité est utile car vous pouvez facilement comparer les résultats de deux modèles différents sur les mêmes données. Vous pouvez également comparer les scores de deux exécutions différentes sur les mêmes données avec des paramètres différents.

1. Connectez la sortie du **jeu de données noté** du module [Scorer le modèle](score-model.md) à l’entrée du module **Évaluer le modèle**. 
2. Connectez la sortie du module Scorer le modèle pour le second modèle à l’entrée de droite du module **Évaluer le modèle**.
3. Cliquez avec le bouton droit sur le module **Évaluer le modèle**, puis sélectionnez **Exécuter la sélection** pour générer les scores d’évaluation.

## <a name="results"></a>Résultats

Après avoir exécuté **Évaluer le modèle**, cliquez avec le bouton droit sur le module et sélectionnez **Résultats de l’évaluation** pour afficher les résultats. Vous pouvez :

+ Enregistrer les résultats dans un jeu de données, pour faciliter l’analyse avec d’autres outils
+ Générer une visualisation dans le concepteur

Si vous connectez des jeux de données aux deux entrées du module **Évaluer le modèle**, les résultats contiennent des métriques pour les deux jeux de données ou les deux modèles.
Le modèle ou les données associés au port de gauche apparaissent en premier dans le rapport, suivis des métriques pour le jeu de données ou le modèle associé au port de droite.  

Par exemple, l’image suivante représente une comparaison des résultats de deux modèles de clustering qui ont été créés sur les mêmes données, mais avec des paramètres différents.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Comme il s’agit d’un modèle de clustering, les résultats de l’évaluation sont différents de ceux obtenus si vous aviez comparé les scores de deux modèles de régression ou comparé deux modèles de classification. La présentation globale est toutefois la même. 

## <a name="metrics"></a>Mesures

Cette section décrit les métriques retournées pour les types spécifiques de modèles pris en charge avec **Évaluer le modèle** :

+ [modèles de classification](#bkmk_classification)
+ [modèles de régression](#bkmk_regression)

###  <a name="bkmk_classification"></a> Métriques pour les modèles de classification

Les métriques suivantes sont rapportées lors de l’évaluation de modèles de classification. Si vous comparez des modèles, elles sont classées selon la métrique que vous sélectionnez pour l’évaluation.  
  
-   L’**exactitude** mesure l’adéquation d’un modèle de classification sous forme de proportion de résultats réels sur le nombre total de cas.  
  
-   La **précision** correspond à la proportion de résultats réels sur tous les résultats positifs.  
  
-   Le **rappel** correspond à la fraction de tous les résultats corrects retournés par le modèle.  
  
-   Le **score F** est calculé comme étant la moyenne pondérée de précision et de rappel comprise entre 0 et 1, où la valeur de score F idéale est 1.  
  
-   **AUC** mesure la zone sous la courbe tracée avec les vrais positifs sur l’axe y et les faux positifs sur l’axe x. Cette métrique est utile car elle fournit un nombre unique qui vous permet de comparer les modèles de types différents.  
  
- La **perte de journaux moyenne** est un score unique utilisé pour exprimer la pénalité des résultats incorrects. Elle est calculée comme étant la différence entre deux distributions de probabilité : la réelle et celle du modèle.  
  
- La **perte de journaux d’apprentissage** est un score unique qui représente l’avantage du classifieur sur une prédiction aléatoire. La perte de journaux mesure l’incertitude de votre modèle en comparant les probabilités qu’il génère avec les valeurs connues (terrestre exacte) dans les étiquettes. Vous souhaitez minimiser la perte de journaux pour l’ensemble du modèle.

##  <a name="bkmk_regression"></a> Métriques pour les modèles de régression
 
Les métriques retournées pour les modèles de régression sont conçues pour estimer la quantité d’erreurs.  Un modèle est jugé correctement adapté aux données si la différence entre valeurs observées et prévues est faible. Toutefois, l’observation du modèle des résidus (la différence entre n’importe quel point prédit et sa valeur réelle correspondante) peut vous en apprendre beaucoup sur un éventuel décalage dans le modèle.  
  
 Les métriques suivantes sont rapportées lors de l’évaluation de modèles de régression. Lorsque vous comparez des modèles, elles sont classées selon la métrique que vous sélectionnez pour l’évaluation.  
  
- L’**erreur absolue moyenne (MAE)** mesure la précision des prédictions par rapport aux résultats réels ; un score inférieur est donc préférence.  
  
- La **racine carrée de l’erreur quadratique moyenne (RMSE)** crée une valeur unique qui résume l’erreur dans le modèle. En mettant la différence au carré, la métrique ne tient pas compte de la différence entre sur-prédiction et sous-prédiction.  
  
- L’**erreur absolue relative (RAE)** est la différence absolue relative entre valeurs prévues et réelles ; elle est relative car la différence moyenne est divisée par la moyenne arithmétique.  
  
- L’**erreur quadratique relative (RSE)** normalise de manière similaire l’erreur quadratique totale des valeurs prévues en divisant par l’erreur quadratique totale des valeurs réelles.  
  
- L’**erreur zéro-un moyenne (MZOE)** indique si la prédiction était correcte ou non.  En d’autres termes : `ZeroOneLoss(x,y) = 1` lorsque `x!=y` ; sinon `0`.
  
- Le **coefficient de détermination**, généralement appelé R<sup>2</sup>, représente la puissance prédictive du modèle sous la forme d’une valeur comprise entre 0 et 1. Zéro signifie que le modèle est aléatoire ; 1 signifie qu’il convient parfaitement. Soyez toutefois vigilant dans l’interprétation de valeurs R<sup>2</sup> car des valeurs faibles peuvent être totalement normales et des valeurs élevées peuvent être suspectes.
  

## <a name="next-steps"></a>Étapes suivantes

Consultez l’[ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 