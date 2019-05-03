---
title: 'Évaluer le modèle : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module évaluer le modèle dans le service Azure Machine Learning pour mesurer la précision d’un modèle formé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028739"
---
# <a name="evaluate-model-module"></a>Évaluer le module de modèle

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour mesurer la précision d’un modèle formé. Vous fournissez un jeu de données qui contient les scores générés à partir d’un modèle et le **évaluer le modèle** module calcule un ensemble de mesures d’évaluation standard du secteur.
  
 Les mesures retournées par **évaluer le modèle** varient selon le type de modèle que vous évaluez :  
  
-   **Modèles de classification**    
-   **Modèles de régression**    



> [!TIP]
> Si vous ne connaissez pas l’évaluation du modèle, nous vous recommandons de la série de vidéos par récupération d’urgence. Stephen Elston, dans le cadre de la [cours d’apprentissage machine](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) à partir de EdX. 


Il existe trois façons d’utiliser le **évaluer le modèle** module :

+ Générer des scores sur vos données d’apprentissage et d’évaluer le modèle basé sur ces scores
+ Générer des scores sur le modèle, mais comparer ces scores aux scores sur un jeu de test réservé
+ Comparer les scores de deux modèles de différentes mais associées, utilisant le même jeu de données

## <a name="use-the-training-data"></a>Utiliser les données d’apprentissage

Pour évaluer un modèle, vous devez connecter un jeu de données qui contient un ensemble de colonnes d’entrée et de scores.  Si aucune donnée n’est disponible, vous pouvez utiliser votre jeu de données d’origine.

1. Connecter le **jeu de données noté** de sortie de la [noter le modèle](./score-model.md) à l’entrée de **évaluer le modèle**. 
2. Cliquez sur **évaluer le modèle** module, puis exécutez l’expérience pour générer les scores d’évaluation.

## <a name="use-testing-data"></a>Utiliser des données de test

Un scénario courant dans l’apprentissage consiste à séparer votre jeu de données d’origine dans l’apprentissage et de test des jeux de données, à l’aide de la [fractionnement](./split-data.md) module, ou le [Partition and Sample](./partition-and-sample.md) module. 

1. Connecter le **jeu de données noté** de sortie de la [noter le modèle](score-model.md) à l’entrée de **évaluer le modèle**. 
2. Connectez la sortie du module fractionner les données qui contient les données de test à l’entrée droite de **évaluer le modèle**.
2. Cliquez sur **évaluer le modèle** module, puis sélectionnez **exécuter sélection** pour générer les scores d’évaluation.

## <a name="compare-scores-from-two-models"></a>Comparer les scores de deux modèles

Vous pouvez également connecter un second jeu de résultats à **évaluer le modèle**.  Les scores peuvent être un ensemble partagé d’évaluation qui a connu des résultats, ou un ensemble de résultats à partir d’un modèle différent pour les mêmes données.

Cette fonctionnalité est utile, car vous pouvez facilement comparer les résultats de deux modèles différents sur les mêmes données. Ou bien, vous pouvez comparer les scores de deux exécutions différentes sur les mêmes données avec des paramètres différents.

1. Connecter le **jeu de données noté** de sortie de la [noter le modèle](score-model.md) à l’entrée de **évaluer le modèle**. 
2. Connectez la sortie du module noter le modèle pour le deuxième modèle à l’entrée droite de **évaluer le modèle**.
3. Avec le bouton droit **évaluer le modèle**, puis sélectionnez **exécuter sélection** pour générer les scores d’évaluation.

## <a name="results"></a>Résultats

Après avoir exécuté **évaluer le modèle**, cliquez sur le module et sélectionnez **résultats d’évaluation** pour afficher les résultats. Vous pouvez :

+ Enregistrer les résultats dans un jeu de données, pour faciliter l’analyse avec d’autres outils
+ Générer une visualisation dans l’interface

Si vous vous connectez des jeux de données pour les deux entrées de **évaluer le modèle**, les résultats contiennent les métriques pour les deux jeux de données, ou les deux modèles.
Le modèle ou les données attachées au port de gauche sont présentées en premier dans le rapport, suivi par les métriques pour le jeu de données, ou le modèle attaché sur le port de droite.  

Par exemple, l’image suivante représente une comparaison des résultats à partir de deux modèles de clustering qui ont été créés sur les mêmes données, mais avec des paramètres différents.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Comme il s’agit d’un modèle de clustering, les résultats d’évaluation sont différentes d’ou si vous comparé les scores de deux modèles de régression par rapport à deux modèles de classification. Toutefois, la présentation globale est le même. 

## <a name="metrics"></a>Mesures

Cette section décrit les mesures retournées pour les types spécifiques de modèles pris en charge pour une utilisation avec **évaluer le modèle**:

+ [modèles de classification](#bkmk_classification)
+ [modèles de régression](#bkmk_regression)

###  <a name="bkmk_classification"></a> Métriques pour les modèles de classification

Les métriques suivantes sont signalées lors de l’évaluation des modèles de classification. Si vous comparez des modèles, ils sont classés par la mesure que vous sélectionnez pour l’évaluation.  
  
-   **Précision** mesure de l’adéquation d’un modèle de classification en tant que la proportion de résultats réels au nombre total de cas.  
  
-   **Précision** est la proportion de résultats réels sur tous les résultats positifs.  
  
-   **Rappelez-vous** est la fraction de tous les résultats corrects retournés par le modèle.  
  
-   **F-score** est calculée comme la moyenne pondérée de précision et le rappel comprise entre 0 et 1, où la valeur de F-score idéale est 1.  
  
-   **AUC** la zone sous la courbe tracée avec des mesures vrais positifs sur les positifs y de l’axe et la valeur false sur l’axe x. Cette mesure est utile, car elle fournit un nombre unique qui vous permet de comparer les modèles de types différents.  
  
- **Nombre moyen de perte logarithmique** est un score unique utilisé pour exprimer la pénalité pour des résultats incorrects. Il est calculé comme la différence entre deux distributions de probabilité : la véritable et celle du modèle.  
  
- **Perte logarithmique d’apprentissage** est un score unique qui représente l’avantage du classifieur sur une prédiction aléatoire. La perte logarithmique mesure l’incertitude quant à votre modèle en comparant les probabilités qu'il génère les valeurs connues (terrain) dans les étiquettes. Vous souhaitez minimiser la perte de journaux pour le modèle dans sa globalité.

##  <a name="bkmk_regression"></a> Métriques pour les modèles de régression
 
Les mesures retournées pour les modèles de régression sont généralement conçus pour estimer la quantité d’erreurs.  Un modèle est considéré comme pour toutes les données correctement si la différence entre les valeurs observées et prévues est petite. Toutefois, examinant le modèle des résidus (la différence entre n’importe quel point prédit une seule et sa valeur réelle correspondante) peut vous indiquer beaucoup sur décalage potentiel dans le modèle.  
  
 Les métriques suivantes sont signalées pour évaluer les modèles de régression. Lorsque vous comparez des modèles, ils sont classés par la mesure que vous sélectionnez pour l’évaluation.  
  
- **Erreur absolue moyenne (MAE)** mesure comment fermer les prédictions sont pour les résultats réels ; par conséquent, un score inférieur est meilleur.  
  
- **Erreur quadratique (RMSE) moyenne** crée une valeur unique qui résume l’erreur dans le modèle. Par la mise au carré de la différence, la métrique ne tient pas compte de la différence entre une prédiction et de prédiction incomplète.  
  
- **Erreur absolue relative (RAE)** est la différence absolue relative entre les valeurs attendues et réelles ; relatif parce que la différence moyenne est divisée par la moyenne arithmétique.  
  
- **Erreur (RSE) quadratique relative** même normalise l’erreur quadratique totale des valeurs prédites en divisant par l’erreur quadratique totale des valeurs réelles.  
  
- **Signifie que zéro une erreur (MZOE)** indique si la prédiction était correcte ou non.  En d’autres termes : `ZeroOneLoss(x,y) = 1` lorsque `x!=y`; sinon `0`.
  
- **Coefficient de détermination**, souvent appelée R<sup>2</sup>, représente la puissance prédictive du modèle sous la forme d’une valeur comprise entre 0 et 1. Zéro signifie que le modèle est aléatoire (explique nothing) ; 1 signifie qu’il convient parfaitement. Toutefois, soyez vigilant dans interprétation R<sup>2</sup> par les valeurs, des valeurs faibles peuvent être entièrement normales et les valeurs élevées peuvent être suspect.
  

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 