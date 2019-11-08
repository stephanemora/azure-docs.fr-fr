---
title: 'Noter le modèle : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Noter le modèle dans Azure Machine Learning pour générer des prédictions à l’aide d’un modèle de classification ou de régression entraîné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e7ab953a7ac0907244ebaab70b3b86cbe6f0f4d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497652"
---
# <a name="score-model-module"></a>Score Model module

Cet article décrit un module dans le concepteur Azure Machine Learning (préversion).

Utilisez ce module pour générer des prédictions à l’aide d’un modèle de classification ou de régression entraîné.

## <a name="how-to-use"></a>Utilisation

1. Ajoutez le module **Modèle de scoring** à votre pipeline.

2. Joignez un modèle entraîné et un jeu de données contenant de nouvelles données d’entrée. 

    Les données doivent être dans un format compatible avec le type de modèle entraîné que vous utilisez. Le schéma du jeu de données d’entrée doit correspondre aussi généralement au schéma des données utilisées pour effectuer l’apprentissage du modèle.

3. Exécuter le pipeline.

## <a name="results"></a>Résultats

Une fois que vous avez généré un jeu de résultats à l’aide du module [Noter le modèle](./score-model.md) :

+ Pour générer un ensemble de mesures servant à évaluer la précision du modèle (performances),  vous pouvez connecter le jeu de données évalué au module [Évaluer le modèle](./evaluate-model.md). 
+ Cliquez avec le bouton droit sur le module et sélectionnez **Visualiser** pour afficher un échantillon des résultats.
+ Enregistrez les résultats dans un jeu de données.

Le score, ou la valeur prédite, peut être indiqué dans différents formats en fonction du modèle et de vos données d’entrée :

- Pour les modèles de classification, le module [Noter le modèle](./score-model.md) génère une valeur prédite pour la classe, ainsi que la probabilité de la valeur prédite.
- Pour les modèles de régression, le module [Noter le modèle](./score-model.md) génère simplement la valeur numérique prédite.
- Pour les modèles de classification d’image, le score peut être la classe d’objet dans l’image ou une valeur booléenne indiquant si une fonctionnalité particulière a été trouvée.

## <a name="publish-scores-as-a-web-service"></a>Publier des scores en tant que service web

Une utilisation courante du scoring consiste à retourner la sortie en tant que partie d’un service web prédictif. Pour plus d’informations, consultez ce didacticiel sur la création d’un service web basé sur un pipeline dans Azure Machine Learning :

## <a name="next-steps"></a>Étapes suivantes

Consultez l’[ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 