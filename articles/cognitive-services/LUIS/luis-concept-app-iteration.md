---
title: Conception d’application itérative
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 67bcb33727bc808f5e5bea701daffc77dde736ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813756"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Cycle de création de votre application LUIS
LUIS apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison. 

![Cycle de création](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Création d’un modèle LUIS
L’objectif du modèle vise à déterminer ce que l’utilisateur demande (intention) et quelles parties de la question fournissent des détails (entités) qui permettent de déterminer la réponse. 

Le modèle doit être spécifique au domaine de l’application afin de déterminer les mots et expressions pertinents, ainsi que l’ordre standard des mots. 

Le modèle inclut une intention et des entités. 

## <a name="add-training-examples"></a>Ajouter des exemples d’apprentissage
LUIS a besoin d’exemples d’énoncés dans les intentions. Les exemples ont besoin d’une variation suffisante en termes de choix des mots et d’ordre des mots pour être en mesure de déterminer à quelle intention est destiné l’énoncé. Dans chaque exemple d’énoncé, les données requises doivent être étiquetées en tant qu’entités. 

Vous demandez à LUIS d’ignorer les énoncés qui ne sont pas pertinents dans le domaine de votre application en affectant l’énoncé à l’intention **None**. Les mots ou expressions que vous n’avez pas besoin d’extraire d’un énoncé n’ont pas besoin d’être étiquetés. Il n’existe aucune étiquette pour les mots ou expressions à ignorer. 

## <a name="train-and-publish-the-app"></a>Former et publier l’application
Une fois que vous avez entre 10 et 15 énoncés différents dans chaque intention, avec les entités requises étiquetées, effectuez l’entraînement et la publication. À partir de la notification de réussite de publication, suivez le lien pour obtenir vos points de terminaison. Veillez à créer votre application et à la publier afin qu’elle soit disponible dans les [régions de point de terminaison](luis-reference-regions.md) requises. 

## <a name="https-endpoint-testing"></a>Test de point de terminaison HTTPS
Vous pouvez tester votre application LUIS à partir du point de terminaison HTTPS. Le test à partir du point de terminaison permet à LUIS de choisir n’importe quel énoncé avec une confiance faible pour la révision.  

## <a name="recycle"></a>Recycler
Lorsque vous avez terminé un cycle de création, vous pouvez recommencer. Commencez par examiner les énoncés de point de terminaison marqués par LUIS avec une confiance faible. Vérifiez ces énoncés en termes d’intention et d’entité. Une fois les énoncés passés en revue, la liste doit être vide.  

## <a name="batch-testing"></a>Test par lot
Le test par lot est un moyen de voir combien d’exemples d’énoncés sont évalués par LUIS. Les exemples doivent être nouveaux pour LUIS et correctement étiquetés avec l’intention et les entités que LUIS doit rechercher. Les résultats des tests indiquent quelles seraient les performances de LUIS sur ce jeu d’énoncés. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de la [collaboration](luis-concept-collaborator.md).
