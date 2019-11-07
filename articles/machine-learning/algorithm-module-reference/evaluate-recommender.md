---
title: 'Évaluer le générateur de recommandations : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Évaluer le générateur de recommandations dans Azure Machine Learning service pour évaluer la précision de prédictions du modèle de générateur de recommandations.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511870"
---
# <a name="evaluate-recommender"></a>Évaluer le générateur de recommandations

Cet article explique comment utiliser le module **Évaluer le générateur de recommandations** du concepteur Azure Machine Learning (préversion) pour mesurer la précision de prédictions effectuées par un modèle de recommandation. Grâce à ce module, vous pouvez évaluer quatre types de recommandations différents :  
  
-   Notations prédites pour un utilisateur et un élément donné  
  
-   Éléments recommandés pour un utilisateur donné  
  
Lorsque vous créez des prédictions à l’aide d’un modèle de recommandation, des résultats légèrement différents sont retournés pour chacun de ces types de prédictions pris en charge. Le module **Évaluer le générateur de recommandations** déduit le type de prédiction du format de colonne du jeu de données noté. Par exemple, le **jeu de données noté** peut contenir :

- triples utilisateur-élément-notation
- utilisateurs et leurs éléments recommandés

Le module applique également les métriques de performances appropriées, en fonction du type de prédiction effectué. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Comment configurer Évaluer le générateur de recommandations

Le module **Évaluer le générateur de recommandations** compare les prédictions générées par un modèle de recommandation avec les données « réelles » correspondantes. Par exemple, le module [Noter le générateur de recommandations SVD](score-svd-recommender.md) produit des jeux de données notés qui peuvent être analysés avec **Évaluer le générateur de recommandations**.

### <a name="requirements"></a>Configuration requise

**Évaluer le générateur de recommandations** requiert les jeux de données suivants comme entrée. 
  
#### <a name="test-dataset"></a>Jeu de données de test

Le **jeu de données de test** contient les données « réelles » sous la forme de **triples utilisateur-élément-notation**.  

#### <a name="scored-dataset"></a>Jeu de données noté

Le **jeu de données noté** contient les prédictions générées par le modèle de recommandation.  
  
Les colonnes de ce second jeu de données dépendent du type de prédiction que vous exécutiez pendant la notation. Par exemple, le jeu de données noté peut contenir l’un des éléments suivants :

- Utilisateurs, éléments et notations que l’utilisateur peut vouloir associer à l’élément
- Liste des utilisateurs et des éléments recommandés pour ceux-ci 

### <a name="metrics"></a>Mesures

Les métriques de performances du modèle sont générées en fonction du type d’entrée. Pour plus de détails, consultez ces sections :

+ [Évaluer les notations prédites](#evaluate-predicted-ratings)
+ [Évaluer les recommandations d’éléments](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Évaluer les notations prédites  

Lors de l’évaluation de notations prédites, le jeu de données noté (la deuxième entrée de **Évaluer le générateur de recommandations**) doit contenir des **triples utilisateur-élément-notation** répondant aux exigences suivantes :
  
-   La première colonne du jeu de données contient des identificateurs d’utilisateur.  
  
-   La deuxième colonne contient les identificateurs d’élément.  
  
-   La troisième colonne contient les notations utilisateur-élément correspondantes.  
  
> [!IMPORTANT] 
> Pour que l’évaluation aboutisse, les noms de colonnes doivent être `User`, `Item` et `Rating`, respectivement.  
  
**Évaluer le générateur de recommandations** compare les notations dans le jeu de données réelles aux notations prédites du jeu de données noté, puis calcule l’**erreur absolue moyenne** (MAE) et la **racine carrée de l’erreur quadratique moyenne** (RMSE).



## <a name="evaluate-item-recommendations"></a>Évaluer les recommandations d’éléments

Lors de l’évaluation d’une recommandation d’élément, utilisez un jeu de données noté incluant les éléments recommandés pour chaque utilisateur :
  
-   La première colonne du jeu de données contient l’identificateur d’utilisateur.  
  
-   Toutes les colonnes suivantes doivent contenir les identificateurs d’éléments recommandés correspondants, classés selon la pertinence d’un élément par rapport à l’utilisateur. 

    Avant de connecter ce jeu de données, nous vous recommandons de trier le jeu de données afin que les éléments les plus pertinents soient en premier.  



> [!IMPORTANT] 
> Pour que **Évaluer le générateur de recommandations** fonctionne, les noms de colonnes doivent être `User`, `Item 1`, `Item 2`, `Item 3`, et ainsi de suite.  
  
**Évaluer le générateur de recommandations** calcule le gain cumulé réduit normalisé moyen (**NDCG**) et le retourne dans le jeu de données de sortie.  
  
Étant donné qu’il est impossible de connaître les notations « réelles » des éléments recommandés, **Évaluer le générateur de recommandations** utilise les notations utilisateur-élément dans le jeu de données de test en tant que gains dans le calcul du NDCG. Pour évaluer, le module d’évaluation du générateur de recommandations doit uniquement produire des recommandations pour les éléments comprenant des notations réelles (dans le jeu de données de test).  
  

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 
