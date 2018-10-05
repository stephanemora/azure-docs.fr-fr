---
title: Passer en revue les énoncés de point de terminaison pour utiliser l’apprentissage actif dans Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: L’apprentissage actif est l’une des trois stratégies pour améliorer la précision de la prédiction et simplifier l’implémentation. Avec l’apprentissage actif, vous passez en revue les énoncés de point de terminaison afin de vérifier que l’intention et l’entité sont correctes. LUIS choisit les énoncés de point de terminaison pour lesquels il a des doutes.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 78cc2a8a2b9295654d0c6264cbf4a4d634b16544
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038168"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Activer l’apprentissage actif en passant en revue les énoncés de point de terminaison
L’apprentissage actif est l’une des trois stratégies pour améliorer la précision de la prédiction et simplifier l’implémentation. Avec l’apprentissage actif, vous passez en revue les énoncés de point de terminaison afin de vérifier que l’intention et l’entité sont correctes. LUIS choisit les énoncés de point de terminaison pour lesquels il a des doutes.

## <a name="what-is-active-learning"></a>Présentation de l’apprentissage actif
L’apprentissage actif est un processus en deux étapes. Tout d’abord, LUIS sélectionne les énoncés qu’il reçoit au niveau du point de terminaison de l’application nécessitant une validation. La deuxième étape est effectuée par le propriétaire de l’application ou un collaborateur pour valider les énoncés sélectionnés pour [révision](luis-how-to-review-endoint-utt.md), y compris l’intention correcte et toutes les entités au sein de l’intention. Après avoir passé en revue les énoncés, formez et publiez de nouveau l’application. 

## <a name="which-utterances-are-on-the-review-list"></a>Quels sont les énoncés dans la liste de révision ?
LUIS ajoute des énoncés à la liste de révision lorsque la première intention de déclenchement a un faible score ou que les scores les deux premières intentions sont trop proches. 

## <a name="single-pool-for-utterances-per-app"></a>Pool unique pour énoncés par application
La liste **Réviser les énoncés de point de terminaison** ne change pas en fonction de la version. Il existe un seul pool d’énoncés à réviser, quelle que soit la version de l’énoncé que vous modifiez ou la version de l’application publiée au point de terminaison. 

## <a name="where-are-the-utterances-from"></a>D’où les énoncés proviennent-ils ?
Les énoncés de point de terminaison proviennent de requêtes de l’utilisateur final sur le point de terminaison HTTP de l’application. Si votre application n’est pas publiée ou n’a pas encore reçu de correspondances, vous n’avez aucun énoncé à réviser. Si aucune correspondance de point de terminaison n’est reçue pour une intention ou une entité spécifique, vous n’avez aucun énoncé les contenant à réviser. 

## <a name="schedule-review-periodically"></a>Planifier une révision régulière
La révision des énoncés suggérés ne doit pas être effectuée chaque jour, mais doit être incluse dans votre maintenance régulière de LUIS. 

## <a name="delete-review-items-programmatically"></a>Supprimer des éléments de révision par programmation
Si votre application est volumineuse, vous pouvez choisir de réviser certains énoncés et de supprimer les autres de la liste par programmation. Pour cela, [obtenez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) tout d’abord la liste, puis [supprimez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) les énoncés par ID.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [révision](luis-how-to-review-endoint-utt.md) des énoncés de point de terminaison
