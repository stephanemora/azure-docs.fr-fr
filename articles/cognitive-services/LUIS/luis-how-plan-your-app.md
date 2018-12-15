---
title: Planifier votre application
titleSuffix: Language Understanding - Azure Cognitive Services
description: Structurez les entités et les intentions d’applications pertinentes, puis créez vos plans d’applications dans Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: diberry
ms.openlocfilehash: e14b9f2930ed9c170f31bd654829efe3b5a99446
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091285"
---
# <a name="plan-your-luis-app"></a>Planifier votre application LUIS

Il est important de planifier votre application. Identifiez votre domaine, notamment les intentions et les entités possibles qui sont pertinentes pour votre application.  

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
Une fois que vous avez déterminé les intentions, créez 10 ou 15 exemples d’énoncés pour chacune. Pour commencer, ne descendez pas en dessous de ce nombre ou créez de nombreux énoncés pour chaque intention. Chaque énoncé doit être différent du précédent. Une sélection adéquate dans les énoncés inclut le nombre total de mots, le choix des mots, le temps des verbes et la ponctuation. 

## <a name="identify-your-entities"></a>Identifier vos entités
Dans les exemples d’énoncés, identifiez les entités à extraire. Pour réserver un vol, vous avez besoin d’informations comme la destination, la date, la compagnie aérienne, la catégorie de ticket et la classe de voyage. Vous créez des entités pour ces types de données, puis marquez les [entités](luis-concept-entity-types.md) dans les exemples d’énoncés, car elles sont importantes pour accomplir une intention. 

Lorsque vous déterminez les entités à utiliser dans votre application, gardez à l’esprit qu’il existe différents types d’entités pour la capture des relations entre les types d’objets. Les [entités dans LUIS](luis-concept-entity-types.md) fournissent plus de détails sur les différents types.

### <a name="simple-entity"></a>Entité simple
Une entité simple décrit un concept unique.

![entité simple](./media/luis-plan-your-app/simple-entity.png)

Pour en savoir plus sur l’extraction de l’entité simple à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#simple-entity-data). Essayez ce [guide de démarrage rapide](luis-quickstart-primary-and-secondary-data.md) pour en savoir plus sur l’utilisation d’une entité simple.

### <a name="hierarchical-entity"></a>Entité hiérarchique
Une entité hiérarchique est un type spécial d’entité **simple** permettant de définir une catégorie et ses membres sous la forme d’une relation parent-enfant. La relation est déterminée par le contexte dans l’énoncé. Les enfants d’une entité hiérarchique sont aussi des entités simples.

![entité hiérarchique](./media/luis-plan-your-app/hierarchical-entity.png)

Pour en savoir plus sur l’extraction de l’entité hiérarchique à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#hierarchical-entity-data). Essayez ce [guide de démarrage rapide](luis-quickstart-intent-and-hier-entity.md) pour en savoir plus sur l’utilisation d’une entité hiérarchique.

### <a name="composite-entity"></a>Entité composite
Une entité composite est constituée d’autres entités qui forment des parties d’un ensemble. Une entité composite contient divers types d’entités.

![entité composite](./media/luis-plan-your-app/composite-entity.png)

Pour en savoir plus sur l’extraction de l’entité composite à partir de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#composite-entity-data). Essayez ce [tutoriel](luis-tutorial-composite-entity.md) pour en savoir plus sur l’utilisation d’une entité composite.

### <a name="prebuilt-entity"></a>Entité prédéfinie
LUIS fournit des [entités prédéfinies](luis-prebuilt-entities.md) pour les types de données courants tels que les données, nombre, adresse e-mail et URL. Vous pouvez utiliser l’entité prédéfinie Number (nombre) pour le nombre de tickets dans une commande de tickets.

![Entité prédéfinie Number (nombre)](./media/luis-plan-your-app/number-entity.png)

Pour plus d’informations sur l’extraction de l’entité prédéfinie à partir de la réponse à la requête JSON du point de terminaison, consultez [Extraction de données](luis-concept-data-extraction.md#prebuilt-entity-data). 

### <a name="list-entity"></a>Entité de liste 
Une entité de liste est une liste de valeurs spécifiée explicitement. Chaque valeur se compose d’un ou plusieurs synonymes. Dans une application de voyage, vous pouvez créer une entité de liste pour représenter les noms des aéroports.

![entité de liste](./media/luis-plan-your-app/list-entity.png)

Pour en savoir plus sur l’extraction d’entités de liste de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#list-entity-data). Essayez ce [guide de démarrage rapide](luis-quickstart-intent-and-list-entity.md) pour en savoir plus sur l’utilisation d’une entité de liste.

### <a name="regular-expression-entity"></a>Entité d’expression régulière
Une entité d’expression régulière permet à LUIS d’extraire des données correctement mises en forme à partir d’un énoncé basé sur une expression régulière.

![Entité d’expression régulière](./media/luis-plan-your-app/regex-entity.png)

Pour en savoir plus sur l’extraction d’entités d’expression régulière de la réponse à la requête JSON du point de terminaison, voir [Extraction de données](luis-concept-data-extraction.md#regular-expression-entity-data). Pour en savoir plus sur l’utilisation d’une entité d’expression régulière, essayez le [démarrage rapide](luis-quickstart-intents-regex-entity.md).

## <a name="next-steps"></a>Étapes suivantes
Une fois que votre application est entraînée et publiée, et obtient les énoncés du point de terminaison, envisagez d’implémenter des améliorations de prédiction avec [l’apprentissage actif](luis-how-to-review-endoint-utt.md), des [listes d’expressions](luis-concept-feature.md) et des [modèles](luis-concept-patterns.md). 


* Consultez [Créer votre première application Language Understanding Intelligent Services (LUIS)](luis-get-started-create-app.md) pour connaître la procédure de création d’une application LUIS.
