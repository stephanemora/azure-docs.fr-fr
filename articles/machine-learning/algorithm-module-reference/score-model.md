---
title: 'Noter le modèle : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module noter le modèle dans le service Azure Machine Learning pour générer des prédictions à l’aide d’une classification formé ou un modèle de régression.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029264"
---
# <a name="score-model-module"></a>Score Model module

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour générer des prédictions à l’aide d’un modèle de classification ou de régression formé.

## <a name="how-to-use"></a>Utilisation

1. Ajouter le **noter le modèle** module à votre expérience.

2. Attacher un modèle formé et un jeu de données contenant les nouvelles données d’entrée. 

    Les données doivent être dans un format compatible avec le type de modèle formé que vous utilisez. Le schéma du jeu de données d’entrée doit correspondre aussi généralement le schéma des données utilisées pour former le modèle.

3. Exécutez l’expérience.

## <a name="results"></a>Résultats

Une fois que vous avez généré un jeu de résultats à l’aide de [noter le modèle](./score-model.md):

+ Pour générer un ensemble de mesures servant à évaluer la précision du modèle (performances).  Vous pouvez connecter le jeu de données évalué à [évaluer le modèle](./evaluate-model.md), 
+ Cliquez sur le module et sélectionnez **visualiser** pour afficher un échantillon des résultats.
+ Enregistrer les résultats dans un jeu de données.

Le score, ou la valeur prédite, peut être dans différents formats, selon le modèle et vos données d’entrée :

- Pour les modèles de classification, [noter le modèle](./score-model.md) génère une valeur prévue pour la classe, ainsi que la probabilité de la valeur prédite.
- Pour les modèles de régression, [noter le modèle](./score-model.md) génère simplement la valeur numérique prévue.
- Pour les modèles de classification d’image, le score peut être la classe d’objet dans l’image ou une valeur booléenne indiquant si une fonctionnalité particulière a été trouvée.

## <a name="publish-scores-as-a-web-service"></a>Publier des scores sous la forme d’un service web

Une utilisation courante de calcul de score consiste à retourner la sortie en tant que partie d’un service web prédictif. Pour plus d’informations, consultez ce didacticiel sur la création d’un service web basé sur une expérience dans Azure Machine Learning :


## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 