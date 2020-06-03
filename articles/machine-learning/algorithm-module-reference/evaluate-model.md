---
title: 'Évaluer le modèle : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Évaluer le modèle dans Azure Machine Learning pour mesurer la justesse d’un modèle entraîné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/24/2020
ms.openlocfilehash: e522291bdf1982ff65a62f028107b15b3249898c
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847410"
---
# <a name="evaluate-model-module"></a>Module Évaluer le modèle

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Utilisez ce module pour mesurer la précision d’un modèle formé. Vous fournissez un jeu de données qui contient les scores générés à partir d’un modèle et le module **Évaluer le modèle** calcule un ensemble de métriques d’évaluation standard du secteur.
  
 Les métriques retournées par **Évaluer le modèle** varient selon le type de modèle que vous évaluez :  
  
-   **Modèles de classification**    
-   **Modèles de régression**  
-   **Modèles de clustering**  


> [!TIP]
> Si vous ne connaissez pas l’évaluation du modèle, nous vous recommandons la série de vidéos du Dr. Stephen Elston, dans le cadre du [cours de Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) à partir d’EdX. 


## <a name="how-to-use-evaluate-model"></a>Comment utiliser le modèle Evaluate
1. Connectez la sortie du **Jeu de données noté** du module [Noter un modèle](./score-model.md) ou la sortie du Jeu de données de résultats du module [Attribuer des données à des clusters](./assign-data-to-clusters.md) au port d’entrée de gauche du module **Évaluer le modèle**. 
  > [!NOTE] 
  > Si vous utilisez des modules tels que « Sélectionner des colonnes dans le jeu de données » pour sélectionner une partie du jeu de données d’entrée, vérifiez que la colonne « Étiquette réelle » (utilisée dans l’apprentissage), la colonne « Probabilités notées » et la colonne « Étiquettes notées » existent pour calculer les métriques telles que l’AUC, la précision pour la classification binaire et la détection des anomalies.
  > Les colonnes « Étiquette réelle » et « Étiquettes notées » existent pour calculer les métriques pour la classification/régression multiclasse.
  > Les colonnes « Attributions », « DistancesToClusterCenter no.X » (X est l’index des centroïdes, qui est compris entre 0, ..., le nombre de centroïdes-1) existent pour calculer les métriques de clustering.

2. [Facultatif] Connectez la sortie du **Jeu de données noté** du module [Noter un modèle](./score-model.md) ou la sortie du Jeu de données de résultats du module Attribuer des données à des clusters au port d’entrée du second modèle de **droite** du module **Évaluer le modèle**. Vous pouvez facilement comparer les résultats de deux modèles différents sur les mêmes données. Les deux algorithmes d'entrée doivent être du même type. Vous pouvez également comparer les scores de deux exécutions différentes sur les mêmes données avec des paramètres différents.

    > [!NOTE]
    > Le type d’algorithme fait référence à 'Classification double classe', 'Classification multiclasse', 'Régression', 'Clustering' sous 'Algorithmes de Machine Learning'. 

3. Envoyez le pipeline pour générer les notes d'évaluation.

## <a name="results"></a>Résultats

Après avoir exécuté **Évaluer le modèle**, sélectionnez le module pour ouvrir le volet de navigation **Évaluer le modèle** à droite.  Ensuite, choisissez l’onglet **Sorties + journaux**, puis, sous cet onglet, la section **Sorties de données** comporte plusieurs icônes.   L’icône **Visualiser** contient une icône de graphique à barres et est une première façon de voir les résultats.

Si vous connectez des jeux de données aux deux entrées du module **Évaluer le modèle**, les résultats contiennent des métriques pour les deux jeux de données ou les deux modèles.
Le modèle ou les données associés au port de gauche apparaissent en premier dans le rapport, suivis des métriques pour le jeu de données ou le modèle associé au port de droite.  

Par exemple, l’image suivante représente une comparaison des résultats de deux modèles de clustering qui ont été créés sur les mêmes données, mais avec des paramètres différents.  

![Comparing2Models](media/module/evaluate-2-models.png)  

Comme il s’agit d’un modèle de clustering, les résultats de l’évaluation sont différents de ceux obtenus si vous aviez comparé les scores de deux modèles de régression ou comparé deux modèles de classification. La présentation globale est toutefois la même. 

## <a name="metrics"></a>Mesures

Cette section décrit les métriques retournées pour les types spécifiques de modèles pris en charge avec **Évaluer le modèle** :

+ [modèles de classification](#metrics-for-classification-models)
+ [modèles de régression](#metrics-for-regression-models)
+ [modèles de clustering](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Métriques pour les modèles de classification

Les métriques suivantes sont rapportées lors de l’évaluation de modèles de classification.
  
-   L’**exactitude** mesure l’adéquation d’un modèle de classification sous forme de proportion de résultats réels sur le nombre total de cas.  
  
-   La **précision** correspond à la proportion de résultats réels sur tous les résultats positifs.  
  
-   Le **rappel** correspond à la fraction de tous les résultats corrects retournés par le modèle.  
  
-   Le **score F** est calculé comme étant la moyenne pondérée de précision et de rappel comprise entre 0 et 1, où la valeur de score F idéale est 1.  
  
-   **AUC** mesure la zone sous la courbe tracée avec les vrais positifs sur l’axe y et les faux positifs sur l’axe x. Cette métrique est utile car elle fournit un nombre unique qui vous permet de comparer les modèles de types différents.  
  
- La **perte de journaux moyenne** est un score unique utilisé pour exprimer la pénalité des résultats incorrects. Elle est calculée comme étant la différence entre deux distributions de probabilité : la réelle et celle du modèle.  
  
- La **perte de journaux d’apprentissage** est un score unique qui représente l’avantage du classifieur sur une prédiction aléatoire. La perte de journaux mesure l’incertitude de votre modèle en comparant les probabilités qu’il génère avec les valeurs connues (terrestre exacte) dans les étiquettes. Vous souhaitez minimiser la perte de journaux pour l’ensemble du modèle.

### <a name="metrics-for-regression-models"></a>Métriques pour les modèles de régression
 
Les métriques retournées pour les modèles de régression sont conçues pour estimer la quantité d’erreurs.  Un modèle est jugé correctement adapté aux données si la différence entre valeurs observées et prévues est faible. Toutefois, l’observation du modèle des résidus (la différence entre n’importe quel point prédit et sa valeur réelle correspondante) peut vous en apprendre beaucoup sur un éventuel décalage dans le modèle.  
  
 Les métriques suivantes sont rapportées lors de l’évaluation de modèles de régression.
  
- L’**erreur absolue moyenne (MAE)** mesure la précision des prédictions par rapport aux résultats réels ; un score inférieur est donc préférence.  
  
- La **racine carrée de l’erreur quadratique moyenne (RMSE)** crée une valeur unique qui résume l’erreur dans le modèle. En mettant la différence au carré, la métrique ne tient pas compte de la différence entre sur-prédiction et sous-prédiction.  
  
- L’**erreur absolue relative (RAE)** est la différence absolue relative entre valeurs prévues et réelles ; elle est relative car la différence moyenne est divisée par la moyenne arithmétique.  
  
- L’**erreur quadratique relative (RSE)** normalise de manière similaire l’erreur quadratique totale des valeurs prévues en divisant par l’erreur quadratique totale des valeurs réelles.  
  

  
- Le **coefficient de détermination**, généralement appelé R<sup>2</sup>, représente la puissance prédictive du modèle sous la forme d’une valeur comprise entre 0 et 1. Zéro signifie que le modèle est aléatoire ; 1 signifie qu’il convient parfaitement. Soyez toutefois vigilant dans l’interprétation de valeurs R<sup>2</sup> car des valeurs faibles peuvent être totalement normales et des valeurs élevées peuvent être suspectes.

###  <a name="metrics-for-clustering-models"></a>Mesures pour les modèles de clustering

Étant donné que les modèles de clustering diffèrent considérablement des modèles de classification et de régression à de nombreux égards, [Évaluer le modèle](evaluate-model.md) retourne également un ensemble différent de statistiques pour les modèles de clustering.  
  
 Les statistiques retournées pour un modèle de clustering décrivent le nombre de points de données attribués à chaque cluster, le degré de séparation entre les clusters et le degré de regroupement des points de données dans chaque cluster.  
  
 Les statistiques du modèle de clustering sont calculées par rapport à la moyenne de l’ensemble du jeu de données, avec des lignes supplémentaires contenant les statistiques par cluster.  
  
Les mesures suivantes sont rapportées lors de l’évaluation de modèles de clustering.
    
-   Les scores de la colonne **Average Distance to Other Center** (distance moyenne à un autre centre) représentent la proximité moyenne de chaque point dans le cluster par rapport aux centroïdes de tous les autres clusters.   

-   Les scores de la colonne **Average Distance to Cluster Center** (distance moyenne au centre du cluster) représentent la proximité de tous les points d’un cluster par rapport au centroïde de ce cluster.  
  
-   La colonne **Number of Points** (Nombre de points) indique le nombre de points de données attribués à chaque cluster, ainsi que le nombre total de points de données dans chaque cluster.  
  
     Si le nombre de points de données attribués aux clusters est inférieur au nombre total de points de données disponibles, cela signifie que les points de données n’ont pas pu être attribués à un cluster.  
  
-   Les scores de la colonne **Maximal Distance to Cluster Center** (Distance maximale au centre du cluster) représentent la somme des distances entre chaque point et le centroïde du cluster de ce point.  
  
     Si ce nombre est élevé, cela peut signifier que le cluster est très dispersé. Vous devez examiner cette statistique en même temps que la **distance moyenne au centre du cluster** pour déterminer la répartition du cluster.   

-   Le score **Combined Evaluation** (Évaluation combinée) en bas de chaque section de résultats répertorie les scores moyens pour les clusters créés dans ce modèle particulier.  
  

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
