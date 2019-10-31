---
title: 'Ajouter des lignes : Référence sur le module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Ajouter des lignes dans Azure Machine Learning service pour concaténer deux jeux de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c8d8b6a873ee17d1658b0cb47de830848b215b89
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693248"
---
# <a name="add-rows-module"></a>Module Ajouter des lignes

Cet article décrit un module de l’interface visuelle (préversion) pour Azure Machine Learning service.

Utilisez ce module pour concaténer deux jeux de données. Dans la concaténation, les lignes du deuxième jeu de données sont ajoutées à la fin du premier jeu de données.  
  
La concaténation de lignes est utile dans des scénarios tels que les suivants :  
  
+ Vous avez généré une série de statistiques d’évaluation, et vous souhaitez les combiner dans une table pour simplifier la génération de rapports.  
  
+ Vous utilisez des jeux de données différents, et vous souhaitez combiner les jeux de données pour créer un jeu de données final.  

## <a name="how-to-use-add-rows"></a>Procédure d’utilisation de Ajouter des lignes  

Pour concaténer des lignes de deux jeux de données, les lignes doivent avoir exactement le même schéma. Cela signifie le même nombre de colonnes et le même type de données dans les colonnes.

1.  Faites glisser le module **Ajouter des lignes** dans votre pipeline, il se trouve sous **Transformation des données**, dans la catégorie **Manipuler**.

2. Connectez les jeux de données aux deux ports d’entrée. Le jeu de données que vous souhaitez ajouter doit être connecté au deuxième port (droite). 
  
3.  Exécuter le pipeline. Le nombre de lignes dans le jeu de données de sortie doit être égal à la somme des lignes de deux jeux de données d’entrée.

    Si vous ajoutez le même jeu de données aux deux entrées du module **Ajouter des lignes**, le jeu de données est dupliqué. 

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 