---
title: 'Ajouter des lignes : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module d’ajouter des lignes dans le service Azure Machine Learning pour concaténer deux jeux de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028754"
---
# <a name="add-rows-module"></a>Ajouter un module de lignes

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour concaténer deux jeux de données. Dans la concaténation, les lignes du deuxième jeu de données sont ajoutées à la fin du premier jeu de données.  
  
La concaténation des lignes est utile dans les scénarios suivants :  
  
+ Vous avez généré une série de statistiques d’évaluation, et que vous souhaitez les combiner dans une table pour la création de rapports plus facile.  
  
+ Vous avez travaillé avec différents jeux de données, et que vous souhaitez combiner les jeux de données pour créer un jeu de données final.  

## <a name="how-to-use-add-rows"></a>L’utilisation d’ajouter des lignes  

Pour concaténer des lignes à partir de deux jeux de données, les lignes doivent avoir exactement le même schéma. Ainsi, le même nombre de colonnes et le même type de données dans les colonnes.

1.  Faites glisser le **Add Rows** module dans votre expérience, vous pouvez le trouver sous **Transformation des données**, dans le **manipuler** catégorie.

2. Connectez les jeux de données pour les deux ports d’entrée. Le jeu de données que vous souhaitez ajouter doit être connecté au deuxième port (à droite). 
  
3.  Exécutez l’expérience. Le nombre de lignes dans le jeu de données de sortie doit être égal à la somme des lignes de deux jeux de données d’entrée.

    Si vous ajoutez le même jeu de données pour les deux entrées de la **Add Rows** module, le jeu de données est dupliqué. 

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 