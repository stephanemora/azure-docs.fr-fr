---
title: 'Former le modèle de Clustering : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module former le modèle de Clustering dans le service Azure Machine Learning pour l’apprentissage des modèles de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028079"
---
# <a name="train-clustering-model"></a>Former le modèle de Clustering

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour former un modèle de clustering.

Le module prend un modèle de clustering non formé que vous avez déjà configuré à l’aide de la [Clustering K-Means](k-means-clustering.md) module et effectue l’apprentissage du modèle à l’aide d’un jeu de données étiqueté ou sans étiquette. Le module crée à la fois un modèle formé que vous pouvez utiliser pour la prédiction et un ensemble d’affectations de cluster pour chaque cas dans les données d’apprentissage.

> [!NOTE]
> Être un clustering ne peut pas de modèle formé à l’aide du [former le modèle](train-model.md) module, qui est le module générique pour l’apprentissage des modèles d’apprentissage automatique. C’est parce que [former le modèle](train-model.md) fonctionne uniquement avec les algorithmes d’apprentissage supervisé. K-means et autres algorithmes de clusters permettent un apprentissage non supervisé, ce qui signifie que l’algorithme puisse apprendre des données sans étiquette.  
  
## <a name="how-to-use-train-clustering-model"></a>L’utilisation de former le modèle de Clustering  
  
1.  Ajouter le **Train Clustering Model** module à votre expérience dans Studio. Vous pouvez rechercher le module sous **Modules Machine Learning**, dans le **Train** catégorie.  
  
2. Ajouter le [Clustering K-Means](k-means-clustering.md) module ou un autre module personnalisé qui crée un clustering compatible de modèle et définissez les paramètres du modèle de clustering.  
    
3.  Joindre un jeu de données de formation à l’entrée droite de **Train Clustering Model**.
  
5.  Dans **jeu de colonnes**, sélectionnez les colonnes du jeu de données à utiliser lors de la création de clusters. Veillez à sélectionner les colonnes qui constituent de bonnes fonctionnalités : par exemple, évitez d’utiliser les ID ou d’autres colonnes qui ont des valeurs uniques ou de colonnes qui ont les mêmes valeurs.

    Si une étiquette est disponible, vous pouvez utiliser comme une fonctionnalité ou les ignorer.  
  
6. Sélectionnez l’option **recherchez Append ou décochez pour résultat uniquement**, si vous souhaitez exporter les données d’apprentissage avec la nouvelle étiquette de cluster.

    Si vous désélectionnez cette option, seuls les affectations de cluster sont générées. 

7. Exécutez l’expérience, ou cliquez sur le **Train Clustering Model** module et sélectionnez **exécuter les éléments sélectionnés**.  
  
### <a name="results"></a>Résultats

Une fois la formation terminée :


+  Pour afficher les valeurs dans le jeu de données, cliquez sur le module, sélectionnez **entraîner des jeux de données**, puis cliquez sur **visualiser**.

+ Pour enregistrer le modèle formé pour une réutilisation ultérieure, cliquez sur le module, sélectionnez **ajouté pour l’apprentissage modèle**, puis cliquez sur **enregistrer en tant que modèle formé**.

+ Pour générer des scores à partir du modèle, utilisez [affecter les données aux Clusters](assign-data-to-clusters.md).



## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 