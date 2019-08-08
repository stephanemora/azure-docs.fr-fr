---
title: Conception d’application itérative - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2a540606a6f9cfa790a2244628e7f0b7bef35986
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639265"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Cycle de création de votre application LUIS
LUIS apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison. 

![Cycle de création](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Création d’un modèle LUIS
L’objectif du modèle vise à déterminer ce que l’utilisateur demande (intention) et quelles parties de la question fournissent des détails (entités) qui permettent de déterminer la réponse. 

Le modèle doit être spécifique au domaine de l’application afin de déterminer les mots et expressions pertinents, ainsi que l’ordre standard des mots. 

Le modèle nécessite des intentions et _doit avoir_ des entités. 

## <a name="add-training-examples"></a>Ajouter des exemples d’apprentissage
LUIS a besoin d’exemples d’énoncés dans les intentions. Les exemples ont besoin d’une variation suffisante en termes de choix des mots et d’ordre des mots pour être en mesure de déterminer à quelle intention est destiné l’énoncé. Dans chaque exemple d’énoncé, les données requises doivent être étiquetées en tant qu’entités. 

Vous demandez à LUIS d’ignorer les énoncés qui ne sont pas pertinents dans le domaine de votre application en affectant l’énoncé à l’intention **None**. Les mots ou expressions que vous n’avez pas besoin d’extraire d’un énoncé n’ont pas besoin d’être étiquetés. Il n’existe aucune étiquette pour les mots ou expressions à ignorer. 

## <a name="train-and-publish-the-app"></a>Former et publier l’application
Une fois que vous avez entre 15 et 30 énoncés différents dans chaque intention, avec les entités requises étiquetées, vous devez effectuer l’[apprentissage](luis-how-to-train.md) et la [publication](luis-how-to-publish-app.md). À partir de la notification de réussite de publication, suivez le lien pour obtenir vos points de terminaison. Veillez à créer et à publier votre application afin qu’elle soit disponible dans les [régions de point de terminaison](luis-reference-regions.md) requises. 

## <a name="https-endpoint-testing"></a>Test de point de terminaison HTTPS
Vous pouvez tester votre application LUIS à partir du point de terminaison HTTPS. Le test à partir du point de terminaison permet à LUIS de choisir n’importe quel énoncé avec une confiance faible pour la [révision](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Recycler

Lorsque vous avez terminé un cycle de création, vous pouvez recommencer. Commencez par [examiner les énoncés de point de terminaison](luis-how-to-review-endpoint-utterances.md) marqués par LUIS avec une confiance faible. Vérifiez ces énoncés en termes d’intention et d’entité. Une fois les énoncés passés en revue, la liste doit être vide.  

Envisagez de [cloner](luis-concept-version.md#clone-a-version) la version actuelle dans une nouvelle version, puis commencez vos modifications de création dans la nouvelle version. 

## <a name="batch-testing"></a>Test par lot

Le [test par lot](luis-concept-batch-test.md) est un moyen de voir combien d’exemples d’énoncés sont évalués par LUIS. Les exemples doivent être nouveaux pour LUIS et correctement étiquetés avec l’intention et les entités que LUIS doit rechercher. Les résultats des tests indiquent quelles seraient les performances de LUIS sur ce jeu d’énoncés. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de la [collaboration](luis-concept-collaborator.md).
