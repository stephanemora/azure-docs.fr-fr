---
title: 'Noter le modèle : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Noter le modèle dans Azure Machine Learning service pour générer des prédictions à l’aide d’un modèle de classification ou de régression entraîné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029264"
---
# <a name="score-model-module"></a>Score Model module

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour générer des prédictions à l’aide d’un modèle de classification ou de régression entraîné.

## <a name="how-to-use"></a>Utilisation

1. Ajoutez le module **Noter le modèle** à votre expérience.

2. Joignez un modèle entraîné et un jeu de données contenant de nouvelles données d’entrée. 

    Les données doivent être dans un format compatible avec le type de modèle entraîné que vous utilisez. Le schéma du jeu de données d’entrée doit correspondre aussi généralement au schéma des données utilisées pour effectuer l’apprentissage du modèle.

3. Exécutez l’expérience.

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

Une utilisation courante du scoring consiste à retourner la sortie en tant que partie d’un service web prédictif. Pour plus d’informations, consultez ce didacticiel sur la création d’un service web basé sur une expérience dans Azure Machine Learning :


## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 