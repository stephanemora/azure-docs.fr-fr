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
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028769"
---
# <a name="assign-data-to-clusters"></a>Affecter des données aux Clusters

Cet article décrit comment utiliser le [affecter les données aux Clusters](assign-data-to-clusters.md) module dans l’interface visuelle, pour générer des prédictions à l’aide d’un modèle de clustering qui a été formé à l’aide de l’algorithme de clustering K-Means.

Le module renvoie un jeu de données qui contient les affectations probables pour chaque nouveau point de données. 


## <a name="how-to-use-assign-data-to-clusters"></a>L’utilisation d’affecter les données aux Clusters
  
1.  Dans l’interface visuelle, recherchez un modèle de clustering formé précédemment. Vous pouvez créer et former un modèle de clustering à l’aide de ces méthodes :  
  
    - Configurer l’algorithme K-means en utilisant le [Clustering K-Means](k-means-clustering.md) module et ensuite former le modèle à l’aide d’un jeu de données et la [former le modèle de Clustering](train-clustering-model.md) module.  
  
  
    Vous pouvez également ajouter un modèle de clustering formé existant à partir de la **modèles enregistrés** groupe dans votre espace de travail.

2. Attacher le modèle formé pour le port d’entrée gauche [affecter les données aux Clusters](assign-data-to-clusters.md).  

3. Attacher un nouveau jeu de données en tant qu’entrée. Dans ce jeu de données, les étiquettes sont facultatives. En règle générale, le clustering est une méthode d’apprentissage non supervisé donc il est peu probable que vous saurez catégories à l’avance.

    Toutefois, les colonnes d’entrée doivent être le même que les colonnes qui ont été utilisées pour l’apprentissage que du modèle de clustering, ou une erreur se produit.

    > [!TIP]
    > Pour réduire le nombre de colonnes générées à partir des prédictions de cluster, utilisez [Select Columns in Dataset](select-columns-in-dataset.md), puis sélectionnez un sous-ensemble de colonnes. 
    
4. Laissez l’option **recherchez Append ou décochez pour résultat uniquement** sélectionné si vous souhaitez que les résultats contiennent le dataset d’entrée complet, avec une colonne indiquant les résultats (affectations de cluster).
  
    Si vous désélectionnez cette option, vous obtenez en retour uniquement les résultats. Cela peut être utile lors de la création des prédictions dans le cadre d’un service web.
  
5.  Exécutez l’expérience.  
  
### <a name="results"></a>Résultats

 
+  Pour afficher les valeurs dans le jeu de données, cliquez sur le module, sélectionnez **entraîner des jeux de données**, puis cliquez sur **visualiser**.

