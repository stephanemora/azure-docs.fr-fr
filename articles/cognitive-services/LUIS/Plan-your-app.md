---
title: Planifier vos applications LUIS | Microsoft Docs
description: Structurez les entités et les intentions d’applications pertinentes, puis créez vos plans d’applications dans Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: DeniseMak
manager: hsalama
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 7aec5d5b90ac7145ce9f337ec74c590b4b88c6b1
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266360"
---
# <a name="plan-your-luis-app"></a>Planifier votre application LUIS

Il est important de planifier votre application avant de commencer à la créer dans LUIS. Préparez un plan ou un schéma des entités et intentions possibles qui sont pertinentes pour le sujet spécifique au domaine de votre application.  

## <a name="identify-your-domain"></a>Identifier votre domaine
Une application LUIS est centrée autour d’un sujet spécifique à un domaine.  Par exemple, vous pouvez avoir une application de voyage qui effectue la réservation de tickets, de vols, d’hôtels et de voitures de location. Une autre application peut fournir du contenu relatif à l’exercice physique, le suivi des efforts de fitness et la définition d’objectifs. 

> [!TIP]
> LUIS offre des [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md) pour de nombreux scénarios courants.
> Vérifiez si vous pouvez utiliser un domaine prédéfini comme point de départ pour votre application.

## <a name="identify-your-intents"></a>Identifier vos intentions
Pensez aux [intentions](luis-concept-intent.md) qui sont importantes pour la tâche de votre application. Prenons l’exemple d’une application de voyage, qui permet de réserver un vol et de vérifier la météo là où se rend l’utilisateur. Vous pouvez définir les intentions « BookFlight » et « GetWeather » pour ces actions. Dans une application plus complexe avec davantage de fonctions, vous avez plus d’intentions, et vous devez les définir soigneusement pour ne pas être trop spécifique. Par exemple, « BookFlight » et « BookHotel » peuvent devoir être des intentions distinctes, mais « BookInternationalFlight » et « BookDomesticFlight » peuvent être trop proches.

> [!NOTE]
> Il est recommandé d’utiliser uniquement les intentions nécessaires pour exécuter les fonctions de votre application. Si vous définissez trop d’intentions, il devient plus difficile pour LUIS de classer les énoncés correctement. Si vous en définissez trop peu, elles peuvent être trop générales au point de se chevaucher.


## <a name="identify-your-entities"></a>Identifier vos entités
Pour réserver un vol, vous avez besoin d’informations comme la destination, la date, la compagnie aérienne, la catégorie de ticket et la classe de voyage. Vous pouvez ajouter ces informations en tant [qu’entités](luis-concept-entity-types.md), car elles sont importantes pour accomplir une intention. 

Lorsque vous déterminez les entités à utiliser dans votre application, gardez à l’esprit qu’il existe différents types d’entités pour la capture des relations entre les types d’objets. Les [entités dans LUIS](luis-concept-entity-types.md) fournissent plus de détails sur les différents types.

### <a name="simple-entity"></a>Entité simple
Une entité simple décrit un concept unique.

![entité simple](./media/luis-plan-your-app/simple-entity.png)

Pour en savoir plus sur l’extraction de l’entité simple à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#simple-entity-data). Pour en savoir plus sur l’utilisation d’une entité simple, essayez le [démarrage rapide](luis-quickstart-primary-and-secondary-data.md) d’entité simple.

### <a name="hierarchical-entity"></a>Entité hiérarchique
Une entité hiérarchique est un type spécial d’entité **simple** permettant de définir une catégorie et ses membres sous la forme d’une relation parent-enfant.

![entité hiérarchique](./media/luis-plan-your-app/hierarchical-entity.png)

Pour en savoir plus sur l’extraction de l’entité hiérarchique à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#hierarchical-entity-data). Pour en savoir plus sur l’utilisation d’une entité hiérarchique, essayez le [démarrage rapide](luis-quickstart-intent-and-hier-entity.md) d’entité hiérarchique.

### <a name="composite-entity"></a>Entité composite
Une entité composite est constituée d’autres entités qui forment des parties d’un ensemble. 

![entité composite](./media/luis-plan-your-app/composite-entity.png)

Pour en savoir plus sur l’extraction de l’entité composite à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#composite-entity-data). Pour en savoir plus sur l’utilisation d’une entité composite, essayez le [didacticiel](luis-tutorial-composite-entity.md) sur les entités composites.

### <a name="prebuilt-entity"></a>Entité prédéfinie
LUIS fournit des [entités prédéfinies](Pre-builtEntities.md) pour les types courants comme `Number`, que vous pouvez utiliser pour le nombre de tickets dans une commande de tickets.

![Entité prédéfinie Number (nombre)](./media/luis-plan-your-app/number-entity.png)

Pour en savoir plus sur l’extraction d’entités d’expression régulière de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#prebuilt-entity-data). 

### <a name="list-entity"></a>Entité de liste 
Une entité de liste est une liste de valeurs spécifiée explicitement. Chaque valeur se compose d’un ou plusieurs synonymes. Dans une application de voyage, vous pouvez créer une entité de liste pour représenter les noms des aéroports.

![entité de liste](./media/luis-plan-your-app/list-entity.png)

Pour en savoir plus sur l’extraction d’entités de liste de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#list-entity-data). Pour en savoir plus sur l’utilisation d’une entité de liste, essayez le [démarrage rapide](luis-quickstart-intent-and-list-entity.md).

### <a name="regular-expression-entity"></a>Entité d’expression régulière
Une entité d’expression régulière permet à LUIS d’extraire des données à partir d’un énoncé basé sur une expression régulière.

![Entité d’expression régulière](./media/luis-plan-your-app/regex-entity.png)

Pour en savoir plus sur l’extraction d’entités d’expression régulière de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#regular-expression-entity-data). Pour en savoir plus sur l’utilisation d’une entité d’expression régulière, essayez le [démarrage rapide](luis-quickstart-intents-regex-entity.md).

## <a name="after-getting-endpoint-utterances"></a>Après l’obtention des énoncés du point de terminaison
Une fois que votre application obtient les énoncés du point de terminaison, envisagez d’implémenter des améliorations de prédiction avec [l’apprentissage actif](label-suggested-utterances.md), des [listes de phrases](luis-concept-feature.md) et des [modèles](luis-concept-patterns.md). 

### <a name="patternany-entity"></a>Entité Pattern.any
Patterns.any est un espace réservé à longueur variable utilisé uniquement dans le gabarit d’énoncé d’un [modèle](luis-concept-patterns.md) pour marquer où l’entité commence et se termine. Les énoncés de modèle respectent une [syntaxe appropriée](luis-concept-patterns.md#pattern-syntax) pour identifier les entités et le texte qui peut être ignoré.


## <a name="next-steps"></a>Étapes suivantes
* Consultez [Créer votre première application Language Understanding Intelligent Services (LUIS)][luis-get-started-create-app] pour connaître la procédure de création d’une application LUIS.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app