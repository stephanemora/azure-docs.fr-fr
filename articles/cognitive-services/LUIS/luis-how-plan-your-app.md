---
title: Planifier votre application - LUIS
titleSuffix: Azure Cognitive Services
description: Structurez les entités et les intentions d’applications pertinentes, puis créez vos plans d’applications dans Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dc648b30dc1236080be06044f510557ae0ce9476
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638316"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>Planifier votre application LUIS avec le domaine du sujet ainsi que les intentions et les entités

Pour planifier votre application, identifiez votre domaine de zone de sujet. Ceci inclut des intentions et des entités possibles qui sont pertinentes pour votre application.  

## <a name="identify-your-domain"></a>Identifier votre domaine

Une application LUIS est centrée autour d’un sujet spécifique à un domaine.  Par exemple, vous pouvez avoir une application de voyage qui effectue la réservation de tickets, de vols, d’hôtels et de voitures de location. Une autre application peut fournir du contenu relatif à l’exercice physique, le suivi des efforts de fitness et la définition d’objectifs. L’identification du domaine vous permet de trouver des mots ou expressions qui sont importants pour votre domaine.

> [!TIP]
> LUIS offre des [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md) pour de nombreux scénarios courants.
> Vérifiez si vous pouvez utiliser un domaine prédéfini comme point de départ pour votre application.

## <a name="identify-your-intents"></a>Identifier vos intentions

Pensez aux [intentions](luis-concept-intent.md) qui sont importantes pour la tâche de votre application. Prenons l’exemple d’une application de voyage, qui permet de réserver un vol et de vérifier la météo là où se rend l’utilisateur. Vous pouvez définir les intentions « BookFlight » et « GetWeather » pour ces actions. Dans une application plus complexe avec davantage de fonctions, vous avez plus d’intentions, et vous devez les définir soigneusement pour ne pas être trop spécifique. Par exemple, « BookFlight » et « BookHotel » peuvent devoir être des intentions distinctes, mais « BookInternationalFlight » et « BookDomesticFlight » peuvent être trop proches.

> [!NOTE]
> Il est recommandé d’utiliser uniquement les intentions nécessaires pour exécuter les fonctions de votre application. Si vous définissez trop d’intentions, il devient plus difficile pour LUIS de classer les énoncés correctement. Si vous en définissez trop peu, elles peuvent être trop générales au point de se chevaucher.

## <a name="create-example-utterances-for-each-intent"></a>Créer des exemples d’énoncés pour chaque intention

Une fois que vous avez déterminé les intentions, créez 15 à 30 exemples d’énoncés pour chacune. Pour commencer, ne descendez pas en dessous de ce nombre ou ne créez pas trop d’énoncés pour chaque intention. Chaque énoncé doit être différent du précédent. Une sélection adéquate dans les énoncés inclut le nombre total de mots, le choix des mots, le temps des verbes et la ponctuation. 

Consultez les [énoncés](luis-concept-utterance.md) pour plus d’informations.

## <a name="identify-your-entities"></a>Identifier vos entités

Dans les exemples d’énoncés, identifiez les entités à extraire. Pour réserver un vol, vous avez besoin d’informations comme la destination, la date, la compagnie aérienne, la catégorie de ticket et la classe de voyage. Créez des entités pour ces types de données, puis marquez les [entités](luis-concept-entity-types.md) dans les exemples d’énoncés, car elles sont importantes pour accomplir une intention. 

Lorsque vous déterminez les entités à utiliser dans votre application, gardez à l’esprit qu’il existe différents types d’entités pour la capture des relations entre les types d’objets. Les [entités dans LUIS](luis-concept-entity-types.md) fournissent plus de détails sur les différents types.

## <a name="next-steps"></a>Étapes suivantes

Une fois que votre application est entraînée et publiée, et obtient les énoncés du point de terminaison, envisagez d’implémenter des améliorations de prédiction avec [l’apprentissage actif](luis-how-to-review-endpoint-utterances.md), des [listes d’expressions](luis-concept-feature.md) et des [modèles](luis-concept-patterns.md). 


* Consultez [Créer votre première application Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) pour connaître la procédure de création d’une application LUIS.
