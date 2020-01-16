---
title: Totaliser les données
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Synthétiser les données dans Azure Machine Learning afin de générer un rapport de statistiques descriptives de base pour les colonnes d’un jeu de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 80e2acf08960796ccb2f3ad87c16674e477c9c00
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428455"
---
# <a name="summarize-data"></a>Totaliser les données

Cet article décrit un module du concepteur Azure Machine Learning (préversion).

Utilisez le module Synthétiser les données pour créer un ensemble de mesures statistiques standard qui décrivent chaque colonne de la table d’entrée.

Les statistiques récapitulatives sont utiles pour comprendre les caractéristiques du jeu de données complet. Par exemple, vous pouvez vous poser les questions suivantes :

- Combien y a-t-il de valeurs manquantes dans chaque colonne ?
- Combien y a-t-il de valeurs uniques dans une colonne de caractéristiques ?
- Quels sont la moyenne et l’écart type de chaque colonne ?

Le module calcule les scores importants de chaque colonne et retourne une ligne de statistiques récapitulatives pour chaque variable (colonne de données) fournie comme entrée.

## <a name="how-to-configure-summarize-data"></a>Comment configurer le module Synthétiser les données  

1. Ajoutez le module **Synthétiser les données** à votre pipeline. Ce module se trouve dans la catégorie **Fonctions statistiques** du concepteur.

1. Connectez le jeu de données pour lequel vous voulez générez un rapport.

    Si vous voulez créer un rapport sur certaines colonnes seulement, utilisez le module [Sélectionner des colonnes dans le jeu de données](select-columns-in-dataset.md) pour projeter un sous-ensemble de colonnes à utiliser.

1. Aucun paramètre supplémentaire n’est nécessaire. Par défaut, le module analyse toutes les colonnes fournies en entrée et, en fonction du type des valeurs dans les colonnes, génère un ensemble de statistiques pertinent, comme décrit dans la section [Résultats](#results).

1. Exécutez le pipeline ou double-cliquez sur le module, et sélectionnez **Exécuter la sélection**.

## <a name="results"></a>Résultats

Le rapport du module peut comprendre les statistiques suivantes. 

|Nom de la colonne|Description|
|------|------|  
|**Fonctionnalité**|Nom de la colonne|
|**Count**|Nombre total des lignes|
|**Unique Value Count**|Nombre de valeurs uniques dans la colonne|
|**Missing Value Count**|Nombre de valeurs uniques dans la colonne|
|**Min**|Valeur la plus basse de la colonne|  
|**Max**|Valeur la plus haute de la colonne|
|**Mean**|Moyenne de toutes les valeurs de colonne|
|**Mean Deviation**|Écart moyen des valeurs de colonne|
|**1st Quartile**|Valeur au premier quartile|
|**Median**|Valeur de colonne médiane|
|**3rd Quartile**|Valeur au troisième quartile|
|**Mode**|Mode des valeurs de colonne|
|**Plage**|Entier représentant le nombre de valeurs comprises entre les valeurs maximale et minimale|
|**Sample Variance**|Variance de la colonne. Voir la section Note|
|**Sample Standard Deviation**|Écart type de la colonne. Voir la section Note|
|**Sample Skewness**|Asymétrie de la colonne. Voir la section Note|
|**Sample Kurtosis**|Kurtosis de la colonne. Voir la section Note|
|**P0.5**|0,5e centile|
|**P1**|1er centile|
|**P5**|5e centile|
|**P95**|95e centile|
|**P99.5**|99,5e centile |

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning.  
