---
title: 'Appliquer une transformation : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Appliquer une transformation dans Azure Machine Learning pour modifier un jeu de données d’entrée en fonction d’une transformation précédemment calculée.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a48ce60dca9f4221e364d53567f5b53719deb18c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314519"
---
# <a name="apply-transformation-module"></a>Appliquer le module de transformation

Cet article décrit un module du concepteur Azure Machine Learning.

Utilisez ce module pour modifier un jeu de données d’entrée en fonction d’une transformation précédemment calculée.  
  
Par exemple, si vous avez utilisé des scores z pour normaliser vos données de formation à l’aide du module **Normaliser les données**, vous pouvez utiliser la valeur de score z qui a été calculée pour la formation pendant la phase de calcul de score également. Dans Azure Machine Learning, vous pouvez enregistrer la méthode de normalisation en tant que transformation, puis utiliser le module **Appliquer une transformation** pour appliquer le score z aux données d’entrée avant le calcul de score.
  
Azure Machine Learning prend en charge la création et l’application de différents types de transformations personnalisées. Par exemple, vous souhaiterez peut-être enregistrer et réutiliser les transformations pour :  
  
- Supprimer ou remplacer des valeurs manquantes, à l’aide du module **Nettoyage des données manquantes**
- Normaliser les données, à l’aide du module **Normaliser les données**
  

## <a name="how-to-use-apply-transformation"></a>Comment utiliser le module Appliquer une transformation  
  
1. Ajoutez le module **Appliquer une transformation** à votre pipeline. Vous pouvez trouver ce module sous **Machine Learning**, dans la catégorie **Score**. 
  
2. Recherchez une transformation existante à utiliser en tant qu’entrée.  Les transformations précédemment enregistrées se trouvent dans le groupe **Transformation** du volet de navigation gauche.  
  
   
  
3. Connectez le jeu de données que vous souhaitez transformer. Le jeu de données doit avoir exactement le même schéma (nombre de colonnes, noms de colonnes, types de données) que le jeu de données pour lequel la transformation a initialement été conçue.  
  
4. Aucun autre paramètre ne doit être défini dans la mesure où toutes les personnalisations s’effectuent lors de la définition de la transformation.  
  
5. Pour appliquer une transformation au nouveau jeu de données, exécutez le pipeline.  

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 