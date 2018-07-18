---
title: Passez en revue les énoncés de point de terminaison pour utiliser l’apprentissage actif dans Language Understanding (LUIS) - Azure | Microsoft Docs
description: Utilisez la fonctionnalité d’apprentissage actif nommée « Réviser les énoncés de point de terminaison » afin d’accélérer les prédictions de performance.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: b9672e8e63fb601d4411a342b7f3c00e30f9e002
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35379133"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Activer l’apprentissage actif en passant en revue les énoncés de point de terminaison
L’apprentissage actif est l’une des trois stratégies pour améliorer la précision de la prédiction et simplifier l’implémentation. 

## <a name="what-is-active-learning"></a>Présentation de l’apprentissage actif
L’apprentissage actif est un processus en deux étapes. Tout d’abord, LUIS sélectionne les énoncés qu’il reçoit au niveau du point de terminaison de l’application nécessitant une validation. La deuxième étape est effectuée par le propriétaire de l’application ou un collaborateur pour valider les énoncés sélectionnés pour [révision](label-suggested-utterances.md), y compris l’intention correcte et toutes les entités au sein de l’intention. Après avoir passé en revue les énoncés, formez et publiez de nouveau l’application. 

## <a name="which-utterances-are-on-the-review-list"></a>Quels sont les énoncés dans la liste de révision ?
LUIS ajoute des énoncés à la liste de révision lorsque la première intention de déclenchement a un faible score ou que les scores les deux premières intentions sont trop proches. 

## <a name="where-are-the-utterances-from"></a>D’où les énoncés proviennent-ils ?
Les énoncés de point de terminaison proviennent de requêtes de l’utilisateur final sur le point de terminaison HTTP de l’application. Si votre application n’est pas publiée ou n’a pas encore reçu de correspondances, vous n’avez aucun énoncé à réviser. Si aucune correspondance de point de terminaison n’est reçue pour une intention ou une entité spécifique, vous n’avez aucun énoncé les contenant à réviser. 

## <a name="schedule-review-periodically"></a>Planifier une révision régulière
La révision des énoncés suggérés ne doit pas être effectuée chaque jour, mais doit être incluse dans votre maintenance régulière de LUIS. 

## <a name="delete-review-items-programmatically"></a>Supprimer des éléments de révision par programmation
Si votre application est volumineuse, vous pouvez choisir de réviser certains énoncés et de supprimer les autres de la liste par programmation. Pour cela, [obtenez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) tout d’abord la liste, puis [supprimez](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) les énoncés par ID.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [révision](Label-Suggested-Utterances.md) des énoncés de point de terminaison
