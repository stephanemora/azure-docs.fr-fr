---
title: Conception itérative d’applications dans LUIS (Language Understanding)
titleSuffix: Azure Cognitive Services
description: LUIS apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.  Les applications LUIS requièrent des itérations de la conception pour former LUIS et obtenir la meilleure extraction de données.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 05f5ceb5a0f3529d7635f7aae0c3c41c19f0b1ad
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031949"
---
# <a name="authoring-cycle"></a>Cycle de création
LUIS apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison. 

![Cycle de création](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Création d’un modèle LUIS
L’objectif du modèle vise à déterminer ce que l’utilisateur demande (intention) et quelles parties de la question fournissent des détails (entités) qui permettent de déterminer la réponse. 

Le modèle doit être spécifique au domaine de l’application afin de déterminer les mots et expressions pertinents, ainsi que l’ordre standard des mots. 

Le modèle inclut une intention et des entités. 

## <a name="add-training-examples"></a>Ajouter des exemples d’apprentissage
LUIS a besoin d’exemples d’énoncés dans les intentions. Les exemples ont besoin d’une variation suffisante en termes de choix des mots et d’ordre des mots pour être en mesure de déterminer à quelle intention est destiné l’énoncé. Dans chaque exemple d’énoncé, les données requises doivent être étiquetées en tant qu’entités. 

Vous demandez à LUIS d’ignorer les énoncés qui ne sont pas pertinents dans le domaine de votre application en affectant l’énoncé à l’intention **None**. Les mots ou expressions que vous n’avez pas besoin d’extraire d’un énoncé n’ont pas besoin d’être étiquetés. Il n’existe aucune étiquette pour les mots ou expressions à ignorer. 

## <a name="train-and-publish-the-app"></a>Entraîner et publier l’application
Une fois que vous avez entre 10 et 15 énoncés différents dans chaque intention, avec les entités requises étiquetées, effectuez l’entraînement et la publication. À partir de la notification de réussite de publication, suivez le lien pour obtenir vos points de terminaison. Veillez à créer votre application et à la publier afin qu’elle soit disponible dans les [régions de point de terminaison](luis-reference-regions.md) requises. 

## <a name="https-endpoint-testing"></a>Test de point de terminaison HTTPS
Vous pouvez tester votre application LUIS à partir du point de terminaison HTTPS. Le test à partir du point de terminaison permet à LUIS de choisir n’importe quel énoncé avec une confiance faible pour la révision.  

## <a name="recycle"></a>Recycler
Lorsque vous avez terminé un cycle de création, vous pouvez recommencer. Commencez par examiner les énoncés de point de terminaison marqués par LUIS avec une confiance faible. Vérifiez ces énoncés en termes d’intention et d’entité. Une fois les énoncés passés en revue, la liste doit être vide.  

## <a name="batch-testing"></a>Test par lot
Le test par lot est un moyen de voir combien d’exemples d’énoncés sont évalués par LUIS. Les exemples doivent être nouveaux pour LUIS et correctement étiquetés avec l’intention et les entités que LUIS doit rechercher. Les résultats des tests indiquent quelles seraient les performances de LUIS sur ce jeu d’énoncés. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts de la [collaboration](luis-concept-collaborator.md).