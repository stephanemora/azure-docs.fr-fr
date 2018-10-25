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
ms.date: 10/07/2018
ms.author: diberry
ms.openlocfilehash: e65e44ec4deb07e85b9514a1685e4dbb692fd512
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857390"
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
Utilisez l’API **[de suppression des énoncés non étiquetés](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)**. Sauvegardez ces énoncés avant suppression en **[exportant les fichiers journaux](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [révision](luis-how-to-review-endoint-utt.md) des énoncés de point de terminaison
