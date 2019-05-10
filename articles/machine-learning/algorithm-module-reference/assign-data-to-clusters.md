---
title: 'Affecter des données au Cluster : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser les données à affecter au module de Cluster dans le service Azure Machine Learning à noter le modèle de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467198"
---
# <a name="module-assign-data-to-clusters"></a>Module : Affecter des données aux Clusters

Cet article décrit comment utiliser le *affecter les données aux Clusters* module dans l’interface visuelle Azure Machine Learning. Le module génère des prédictions via un modèle de clustering qui a été formé avec le *clustering K-means* algorithme.

Les données à affecter au module de Clusters retourne un jeu de données qui contient les affectations probables pour chaque nouveau point de données. 


## <a name="how-to-use-assign-data-to-clusters"></a>L’utilisation d’affecter les données aux Clusters
  
1. Dans l’interface visuelle de Azure Machine Learning, recherchez un modèle de clustering formé précédemment. Vous pouvez créer et former un modèle de clustering à l’aide d’une des méthodes suivantes :  
  
    - Configurer l’algorithme de clustering K-means à l’aide de la [Clustering K-Means](k-means-clustering.md) module et former le modèle à l’aide d’un jeu de données et le module former le modèle de Clustering (cet article).  
  
    - Vous pouvez également ajouter un modèle de clustering formé existant à partir de la **modèles enregistrés** groupe dans votre espace de travail.

2. Attacher le modèle formé pour le port d’entrée gauche **affecter les données aux Clusters**.  

3. Attacher un nouveau jeu de données en tant qu’entrée. 

   Dans ce jeu de données, les étiquettes sont facultatives. En règle générale, le clustering est une méthode d’apprentissage non supervisé. Vous êtes censé pas connaître les catégories à l’avance. Toutefois, les colonnes d’entrée doivent être le même que les colonnes qui ont été utilisées pour l’apprentissage que du modèle de clustering, ou une erreur se produit.

    > [!TIP]
    > Pour réduire le nombre de colonnes qui sont écrites dans l’interface à partir des prédictions de cluster, utilisez [sélectionner des colonnes dans le jeu de données](select-columns-in-dataset.md), puis sélectionnez un sous-ensemble de colonnes. 
    
4. Laissez le **recherchez Append ou décochez pour résultat uniquement** case cochée si vous souhaitez que les résultats contiennent le dataset d’entrée complet, y compris une colonne qui affiche les résultats (affectations de cluster).
  
    Si vous désactivez cette case à cocher, seuls les résultats sont retournés. Cette option peut être utile lorsque vous créez des prédictions dans le cadre d’un service web.
  
5.  Exécutez l’expérience.  
  
### <a name="results"></a>Résultats

+  Pour afficher les valeurs dans le jeu de données, cliquez sur le module, sélectionnez **entraîner des jeux de données**, puis sélectionnez **visualiser**.

