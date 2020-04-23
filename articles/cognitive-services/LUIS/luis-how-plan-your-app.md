---
title: Planifier votre application - LUIS
description: Structurez les entités et les intentions d’applications pertinentes, puis créez vos plans d’applications dans Language Understanding Intelligent Services (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: dfed27a05973a2ea2e9a97eaa1c233b847b33d87
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382304"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>Planifier votre schéma d’application LUIS avec le domaine de l’objet et l’extraction de données

Un schéma d’application LUIS contient des [intentions](luis-glossary.md#intent) et des [entités](luis-glossary.md#entity) pertinentes pour votre [domaine](luis-glossary.md#domain) de sujet. Les intentions classifient les [énoncés](luis-glossary.md#utterance) utilisateur et les entités extraient les données des énoncés utilisateur.

## <a name="identify-your-domain"></a>Identifier votre domaine

Une application LUIS est centrée sur un domaine de sujets. Par exemple, vous pouvez avoir une application de voyage qui gère la réservation de tickets, de vols, d’hôtels et de voitures de location. Une autre application peut fournir du contenu relatif à l’exercice physique, le suivi des efforts de fitness et la définition d’objectifs. L’identification du domaine vous permet de trouver des mots ou expressions qui sont pertinents pour votre domaine.

> [!TIP]
> LUIS offre des [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md) pour de nombreux scénarios courants. Vérifiez si vous pouvez utiliser un domaine prédéfini comme point de départ pour votre application.

## <a name="identify-your-intents"></a>Identifier vos intentions

Pensez aux [intentions](luis-concept-intent.md) qui sont importantes pour la tâche de votre application.

Prenons l’exemple d’une application de voyage, qui permet de réserver un vol et de vérifier la météo là où se rend l’utilisateur. Vous pouvez définir les intentions `BookFlight` et `GetWeather` pour ces actions.

Dans une application plus complexe avec davantage de fonctions, vous avez plus d’intentions et vous devez les définir soigneusement pour ne pas être trop spécifique. Par exemple, `BookFlight` et `BookHotel` doivent peut-être être des intentions distinctes, mais `BookInternationalFlight` et `BookDomesticFlight` peuvent être trop similaires.

> [!NOTE]
> Il est recommandé d’utiliser uniquement les intentions nécessaires pour exécuter les fonctions de votre application. Si vous définissez trop d’intentions, il devient plus difficile pour LUIS de classer les énoncés correctement. Si vous en définissez trop peu, elles peuvent être trop générales au point de se chevaucher.

Si vous n’avez pas besoin d’identifier l’intention globale de l’utilisateur, ajoutez tous les exemples d’énoncés utilisateur à l’intention `None`. Si votre application a besoin de plus d’intentions, vous pouvez les créer ultérieurement.

## <a name="create-example-utterances-for-each-intent"></a>Créer des exemples d’énoncés pour chaque intention

Pour commencer, évitez de créer un trop grand nombre d’énoncés pour chaque intention. Une fois que vous avez déterminé les intentions, créez 15 à 30 exemples d’énoncés par intention. Chaque énoncé doit être différent de ceux fournis précédemment. Une sélection adéquate dans les énoncés inclut le nombre total de mots, le choix des mots, le temps des verbes et la [ponctuation](luis-reference-application-settings.md#punctuation-normalization).

Pour plus d’informations, consultez [Connaître les bons énoncés pour les applications LUIS](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Identifier vos entités

Dans les exemples d’énoncés, identifiez les entités à extraire. Pour réserver un vol, vous avez besoin d’informations comme la destination, la date, la compagnie aérienne, la catégorie de ticket et la classe de voyage. Créez des entités pour ces types de données, puis marquez les [entités](luis-concept-entity-types.md) dans les exemples d’énoncés. Les entités sont importantes pour accomplir une intention.

Lorsque vous déterminez les entités à utiliser dans votre application, gardez à l’esprit qu’il existe différents types d’entités pour la capture des relations entre les types d’objet. Les [entités dans LUIS](luis-concept-entity-types.md) fournissent plus de détails sur les différents types.

> [!TIP]
> LUIS offre des [entités prédéfinies](luis-prebuilt-entities.md) pour les scénarios courants d’utilisateur conversationnel. Envisagez d’utiliser des entités prédéfinies comme point de départ pour le développement de votre application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Apprentissage du cycle de vie de développement LUIS](luis-concept-app-iteration.md)

