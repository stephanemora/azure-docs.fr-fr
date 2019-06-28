---
title: 'Entraîner un modèle de clustering : informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Train Clustering Model (Entraîner un modèle de clustering) dans le service Azure Machine Learning pour entraîner les modèles de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028079"
---
# <a name="train-clustering-model"></a>Entraîner un modèle de clustering

Cet article décrit un module de l’interface visuelle (préversion) du service Azure Machine Learning.

Utilisez ce module pour entraîner un modèle de clustering.

Le module prend un modèle de clustering non entraîné que vous avez déjà configuré à l’aide du module [Clustering k-moyennes](k-means-clustering.md) et l’entraîne à l’aide d’un jeu de données étiqueté ou sans étiquette. Le module crée à la fois un modèle entraîné que vous pouvez utiliser à des fins de prédiction et un ensemble d’affectations de cluster pour chaque cas dans les données d’apprentissage.

> [!NOTE]
> Il est impossible d’entraîner un modèle de clustering à l’aide du module [Train Model](train-model.md) (Entraîner le modèle), qui est le module générique pour l’entraînement des modèles d’apprentissage automatique. Cela est dû au fait que le module [Train Model](train-model.md) (Entraîner le modèle) fonctionne uniquement avec les algorithmes d’apprentissage supervisé. L’algorithme de clustering k-moyennes et les autres algorithmes de clustering permettent un apprentissage non supervisé, ce qui signifie que l’algorithme peut apprendre à partir de données sans étiquette.  
  
## <a name="how-to-use-train-clustering-model"></a>Entraînement d’un modèle de clustering  
  
1.  Ajoutez le module **Train Clustering Model** (Entraîner un modèle de clustering) à votre expérience dans Studio. Vous trouverez ce module sous **Modules Machine Learning**, dans la catégorie **Entraîner**.  
  
2. Ajouter le module [Clustering k-moyennes](k-means-clustering.md) ou un autre module personnalisé qui crée un modèle de clustering compatible, puis définissez les paramètres du modèle de clustering.  
    
3.  Joignez un jeu de données d’entraînement à l’entrée à droite du module **Train Clustering Model** (Entraîner un modèle de clustering).
  
5.  Dans **Jeu de colonnes**, sélectionnez les colonnes du jeu de données à utiliser lors de la création de clusters. Veillez à sélectionner les colonnes qui constituent de bonnes fonctionnalités : par exemple, évitez d’utiliser les ID ou d’autres colonnes qui ont des valeurs uniques ou des colonnes qui ont toutes les mêmes valeurs.

    Si une étiquette est disponible, vous pouvez l’utiliser comme une fonctionnalité ou l’ignorer.  
  
6. Sélectionnez l’option **Check for Append or Uncheck for Result Only** (Cocher pour ajouter ou décocher pour les résultats uniquement), si vous souhaitez exporter les données d’apprentissage avec la nouvelle étiquette de cluster.

    Si vous désélectionnez cette option, seuls les affectations de cluster sont générées. 

7. Exécutez l’expérience, ou cliquez sur le module **Train Clustering Model** (Entraîner un modèle de clustering) et sélectionnez **Exécuter la sélection**.  
  
### <a name="results"></a>Résultats

Une fois l’apprentissage terminé :


+  Pour afficher les valeurs dans le jeu de données, cliquez sur le module avec le bouton droit de la souris, sélectionnez **Result datasets** (Jeux de données de résultats), puis cliquez sur **Visualiser**.

+ Pour enregistrer le modèle entraîné en vue d’une réutilisation ultérieure, cliquez sur le module, sélectionnez **Modèle formé**, puis cliquez sur **Save As Trained Model** (Enregistrer en tant que modèle formé).

+ Pour générer des scores à partir du modèle, utilisez l’option [Attribuer des données à des clusters](assign-data-to-clusters.md).



## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour le service Azure Machine Learning. 