---
title: Passer en revue les énoncés de l’utilisateur – LUIS
titleSuffix: Azure Cognitive Services
description: Avec l’apprentissage actif, vous passez en revue les énoncés de point de terminaison afin de vérifier que l’intention et l’entité sont correctes. LUIS choisit les énoncés de point de terminaison pour lesquels il a des doutes.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218783"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Concepts pour l’activation de l’apprentissage actif en passant en revue les énoncés de point de terminaison
L’apprentissage actif est l’une des trois stratégies pour améliorer la précision de la prédiction et simplifier l’implémentation. Avec l’apprentissage actif, vous passez en revue les énoncés de point de terminaison afin de vérifier que l’intention et l’entité sont correctes. LUIS choisit les énoncés de point de terminaison pour lesquels il a des doutes.

## <a name="what-is-active-learning"></a>Présentation de l’apprentissage actif
L’apprentissage actif est un processus en deux étapes. Tout d’abord, LUIS sélectionne les énoncés qu’il reçoit au niveau du point de terminaison de l’application nécessitant une validation. La deuxième étape est effectuée par le propriétaire de l’application ou un collaborateur pour valider les énoncés sélectionnés pour [révision](luis-how-to-review-endpoint-utterances.md), y compris l’intention correcte et toutes les entités au sein de l’intention. Après avoir passé en revue les énoncés, formez et publiez de nouveau l’application.

## <a name="which-utterances-are-on-the-review-list"></a>Quels sont les énoncés dans la liste de révision ?
LUIS ajoute des énoncés à la liste de révision lorsque la première intention de déclenchement a un faible score ou que les scores les deux premières intentions sont trop proches.

## <a name="single-pool-for-utterances-per-app"></a>Pool unique pour énoncés par application
La liste **Réviser les énoncés de point de terminaison** ne change pas en fonction de la version. Il existe un seul pool d’énoncés à réviser, quelle que soit la version de l’énoncé que vous modifiez ou la version de l’application publiée au point de terminaison.

## <a name="where-are-the-utterances-from"></a>D’où les énoncés proviennent-ils ?
Les énoncés de point de terminaison proviennent de requêtes de l’utilisateur final sur le point de terminaison HTTP de l’application. Si votre application n’est pas publiée ou n’a pas encore reçu de correspondances, vous n’avez aucun énoncé à réviser. Si aucune correspondance de point de terminaison n’est reçue pour une intention ou une entité spécifique, vous n’avez aucun énoncé les contenant à réviser.

## <a name="schedule-review-periodically"></a>Planifier une révision régulière
La révision des énoncés suggérés ne doit pas être effectuée chaque jour, mais doit être incluse dans votre maintenance régulière de LUIS.

## <a name="delete-review-items-programmatically"></a>Supprimer des éléments de révision par programmation
Utilisez l’API **[de suppression des énoncés non étiquetés](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Sauvegardez ces énoncés avant suppression en **[exportant les fichiers journaux](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="enable-active-learning"></a>Activer l’apprentissage actif

Pour activer l’apprentissage actif, vous devez enregistrer des requêtes utilisateur. Pour cela, vous appelez la [requête de point de terminaison](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) avec la valeur et le paramètre querystring `log=true`.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [révision](luis-how-to-review-endpoint-utterances.md) des énoncés de point de terminaison
