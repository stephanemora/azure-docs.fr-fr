---
title: 'Appliquer une transformation : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Appliquer une transformation dans Azure Machine Learning pour modifier un jeu de données d’entrée en fonction d’une transformation précédemment calculée.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395257"
---
# <a name="apply-transformation-module"></a>Appliquer le module de transformation

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Utilisez ce module pour modifier un jeu de données d’entrée en fonction d’une transformation précédemment calculée.

Par exemple, si vous avez utilisé des scores z pour normaliser vos données de formation à l’aide du module **Normaliser les données**, vous pouvez utiliser la valeur de score z qui a été calculée pour la formation pendant la phase de calcul de score également. Dans Azure Machine Learning, vous pouvez enregistrer la méthode de normalisation en tant que transformation, puis utiliser le module **Appliquer une transformation** pour appliquer le score z aux données d’entrée avant le calcul de score.

## <a name="how-to-save-transformations"></a>Enregistrer des transformations

Le concepteur vous permet d’enregistrer des transformations de données, comme des **jeux de données**, pour vous permettre de les utiliser dans d'autres pipelines.

1. Sélectionnez un module de transformation de données qui s’est correctement exécuté.

1. Sélectionnez l'onglet **Sorties + journaux d'activité**.

1. Sélectionnez l’**icône Enregistrer** pour enregistrer la **Transformation de résultat**.

## <a name="how-to-use-apply-transformation"></a>Comment utiliser le module Appliquer une transformation  
  
1. Ajoutez le module **Appliquer une transformation** à votre pipeline. Ce module figure dans la section **Scoring et évaluation d’un modèle** de la palette des modules. 
  
1. Recherchez la transformation enregistrée souhaitée sous **Jeux de données** > **Mes jeux de données** dans la palette de modules.

1. Connectez la sortie de la transformation enregistrée au port d’entrée gauche du module **Appliquer une transformation**.

    Le jeu de données doit avoir exactement le même schéma (nombre de colonnes, noms de colonnes, types de données) que le jeu de données pour lequel la transformation a initialement été conçue.  
  
1. Connectez la sortie du jeu de données du module souhaité au port d’entrée droit du module **Appliquer une transformation**.
  
1. Pour appliquer une transformation au nouveau jeu de données, exécutez le pipeline.  

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 