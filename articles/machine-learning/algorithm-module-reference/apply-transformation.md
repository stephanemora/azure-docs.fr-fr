---
title: 'Appliquer la Transformation : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module appliquer une Transformation dans le service Azure Machine Learning pour modifier un jeu de données d’entrée selon une transformation précédemment calculée.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028709"
---
# <a name="apply-transformation-module"></a>Appliquer le module de transformation

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour modifier un jeu de données d’entrée selon une transformation précédemment calculée.  
  
Par exemple, si vous avez utilisé des scores-z pour normaliser vos données d’apprentissage à l’aide de la **normaliser les données** module, vous pouvez utiliser la valeur z-score qui a été calculée pour l’apprentissage pendant la phase de calcul de score. Dans Azure Machine Learning, vous pouvez enregistrer la méthode de normalisation ainsi une transformation, puis en utilisant **Apply Transformation** pour appliquer le z-score pour les données d’entrée avant le calcul de score.
  
Azure Machine Learning prend en charge pour la création et d’appliquer différents types de transformations personnalisées. Par exemple, vous souhaiterez peut-être enregistrer et de réutiliser ensuite les transformations à :  
  
- Supprimer ou remplacer les valeurs manquantes, à l’aide de **nettoyer les données manquantes**
- Normaliser les données, à l’aide de **normaliser les données**
  

## <a name="how-to-use-apply-transformation"></a>Comment utiliser Apply Transformation  
  
1. Ajouter le **Apply Transformation** module à votre expérience. Vous trouverez ce module sous **Machine Learning**, dans le **Score** catégorie. 
  
2. Recherchez une transformation existante à utiliser en tant qu’entrée.  Vous trouverez les transformations précédemment enregistrées dans le **transforme** groupe dans le volet de navigation gauche.  
  
   
  
3. Connectez le jeu de données que vous souhaitez transformer. Le jeu de données doit avoir exactement le même schéma (nombre de colonnes, les noms de colonnes, les types de données) en tant que le jeu de données pour lequel la transformation a été conçue tout d’abord.  
  
4. Aucun autre paramètre ne doivent être définies dans la mesure où toutes les personnalisations s’effectue lors de la définition de la transformation.  
  
5. Pour appliquer une transformation au nouveau jeu de données, exécutez l’expérience.  

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 