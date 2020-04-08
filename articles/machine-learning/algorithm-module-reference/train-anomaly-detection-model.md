---
title: 'Effectuer l’apprentissage du modèle de détection d’anomalie : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Effectuer l’apprentissage du modèle de détection d’anomalie pour créer un modèle de détection d’anomalie formé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79504073"
---
# <a name="train-anomaly-detection-model"></a>Entraîner un modèle de détection des anomalies

Cet article explique comment utiliser le module **Effectuer l’apprentissage du modèle de détection d’anomalie** dans le concepteur Azure Machine Learning (préversion) pour créer un modèle de détection d’anomalie formé.

Le module prend comme entrée un ensemble de paramètres de modèle pour le modèle de détection d’anomalie et un jeu de données sans étiquettes. Il renvoie un modèle de détection d’anomalie formé, ainsi qu’un jeu d’étiquettes pour les données d’apprentissage.  

Pour plus d’informations sur les algorithmes de détection d’anomalie fournis dans le concepteur, consultez les rubriques suivantes : 

+ [Détection d’anomalie basée sur l’analyse en composantes principales (ACP)](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Comment configurer le module Effectuer l’apprentissage du modèle de détection d’anomalie 

1.  Ajoutez le module **Effectuer l’apprentissage du modèle de détection d’anomalie** à votre pipeline dans le concepteur. Ce module figure dans la catégorie **Détection d’anomalie**.

2. Connectez l’un des modules conçus pour la détection d’anomalie, par exemple le module [Détection d’anomalie basée sur l’ACP](pca-based-anomaly-detection.md).

    Les autres types de modèles ne sont pas pris en charge ; lors de l’exécution du pipeline, vous obtiendrez l’erreur : Tous les modèles doivent avoir le même type d’apprenant.  

3.  Configurez le module de détection d’anomalie en choisissant la colonne d’étiquette et en définissant d’autres paramètres spécifiques à l’algorithme.  

4.  Joignez un jeu de données d’apprentissage à l’entrée à droite du module **Effectuer l’apprentissage du modèle de détection d’anomalie**.  

5.  Envoyez le pipeline.  

## <a name="results"></a>Résultats

Une fois l’apprentissage terminé :

+ Pour afficher les paramètres du modèle, cliquez avec le bouton droit sur le module et sélectionnez **Visualiser**. 

+ Pour créer des prédictions, utilisez [Noter le modèle](score-model.md) avec les nouvelles données d’entrée.

+ Pour enregistrer un instantané du modèle formé, sélectionnez le module, puis cliquez sur l’icône **Inscrire le jeu de données** sous **Sorties+journaux** dans le panneau droit.   

 
## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 

Pour obtenir la liste des erreurs spécifiques aux modules du concepteur, consultez [Exceptions et codes d’erreur du concepteur (préversion)](designer-error-codes.md).
'