---
title: Fonctionnalités – LUIS
titleSuffix: Azure Cognitive Services
description: Ajoutez des fonctionnalités à un modèle de langage afin de fournir des conseils sur la façon de reconnaître les entrées que vous souhaitez étiqueter ou classer.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: e85f9a90af778a7e68bf761d8d2159e808cf122d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639208"
---
# <a name="phrase-list-features-in-your-luis-app"></a>Caractéristiques de liste d’expressions dans votre application LUIS

En Machine Learning, une *caractéristique* (« feature ») est un trait ou un attribut distinctif des données observées par le système. 

Ajoutez des fonctionnalités à un modèle de langage afin de fournir des conseils sur la façon de reconnaître les entrées que vous souhaitez étiqueter ou classer. Les fonctionnalités aident LUIS à reconnaître les intentions et les entités, mais les fonctionnalités ne sont pas elles-mêmes des intentions ou des entités. Les fonctionnalités peuvent toutefois fournir des exemples de termes connexes.  

## <a name="what-is-a-phrase-list-feature"></a>Qu’est-ce qu’une fonctionnalité de liste d’expressions ?
Une liste d’expressions est une liste de mots ou d’expressions qui sont importants pour votre application, c’est-à-dire, davantage que les autres mots énoncés. Une liste d’expressions complète le vocabulaire du domaine d’application sous la forme d’un signal supplémentaire émis vers LUIS au sujet de ces mots. Ce que LUIS apprend sur l’un d’entre elles s’applique automatiquement également aux autres. Cette liste n’est pas une [entité de liste](luis-concept-entity-types.md#types-of-entities) fermée de correspondances de texte exactes.

Les listes d’expressions n’aident pas à la recherche de radical. Vous devez donc ajouter des exemples d’énoncés qui utilisent plusieurs types de recherches de radical pour les mots ou expressions importants.

## <a name="phrase-lists-help-all-models"></a>Les listes d’expressions aident tous les modèles

Les listes d’expressions ne sont pas liées à une intention ou à une entité spécifique. Elles sont ajoutées pour améliorer la détection de toutes les intentions et entités. Leur objectif est d’améliorer la détection de l’intention et la classification des entités.

## <a name="how-to-use-phrase-lists"></a>Comment utiliser des listes d’expressions

Vous pouvez [créer une liste d’expressions](luis-how-to-add-features.md) lorsque votre application comprend des mots ou des expressions qui sont importants pour elle, par exemple :

* Terminologie du secteur
* Argot
* Abréviations
* Termes propres à l’entreprise
* Mots provenant d’une autre langue, mais qui sont fréquemment utilisés dans votre application
* Mots clés et expressions dans vos exemples d’énoncés

Une fois que vous avez entré plusieurs mots ou expressions, utilisez la caractéristique **Recommandation** pour obtenir les valeurs associées. Passez en revue ces valeurs avant de les ajouter à votre liste d’expressions.

|Type de liste|Objectif|
|--|--|
|Interchangeable|Synonymes ou mots qui, quand ils sont remplacés par un autre mot dans la liste, ont la même intention et extraction d’entité.|
|Non-interchangeable|Vocabulaire d’application propre à votre application (généralement plus que d’autres mots dans cette langue).|

### <a name="interchangeable-lists"></a>Listes d’expressions interchangeables

Une liste d’expressions *interchangeables* est constituée de synonymes. Par exemple, si vous souhaitez obtenir tous les synonymes d’étendues d’eau et que vous disposez des exemples d’énoncés suivants : 

* Quelles sont les villes qui sont proches des Grands Lacs ? 
* Quelle est la route qui longe les rives de Lake Havasu ?
* Où naît et où se jette le Nil ? 

Chaque énoncé doit être déterminé à la fois par l’intention et par les entités, quel que soit le type de l’étendue d’eau : 

* Quelles sont les villes qui sont proches de [Étendue d’eau] ?
* Quelle est la route qui longe les rives de [Étendue d’eau] ?
* Où naît et où se jette [Étendue d’eau] ? 

Puisque les mots et les expressions relatifs à l’étendue d’eau sont synonymes et peuvent être utilisés indifféremment dans les énoncés, utilisez le paramètre **Interchangeable** dans la liste d’expressions. 

### <a name="non-interchangeable-lists"></a>Listes d’expressions non interchangeables

Une liste d’expressions non interchangeables est un signal qui améliore la détection LUIS. La liste d’expressions indique les mots ou les expressions qui sont plus importants que les autres. Cela aide à la fois à déterminer l’intention et à détecter les entités. Par exemple, supposons que vous ayez un domaine tel que le voyage qui soit « mondial » (c’est-à-dire qui englobe plusieurs cultures, tout en restant dans une même langue). Il existe des mots et des expressions qui sont importants pour l’application, mais qui ne sont pas synonymes. 

Prenons un autre exemple : l’utilisation d’une liste d’expressions non interchangeables pour les mots rares, propriétaires et étrangers. LUIS peut ne pas être en mesure de reconnaître des mots rares et propriétaires, ainsi que des mots étrangers (en dehors de la culture de l’application). Le paramètre non interchangeable indique que l’ensemble de mots rares constitue une classe que LUIS doit apprendre à reconnaître, mais que ces mots ne sont ni synonymes, ni interchangeables entre eux.

N’ajoutez pas tous les mots ou expressions possibles à la liste d’expressions. N’ajoutez que quelques mots ou expressions à la fois, effectuez un nouvel entraînement, puis publiez. 

À mesure que la liste d’expressions s’agrandit, vous constaterez peut-être que certains termes ont de nombreuses formes (synonymes). Répartissez-les dans une autre liste d’expressions interchangeable. 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="phrase-lists-help-identify-simple-interchangeable-entities"></a>Les listes d’expressions permettent de détecter les entités interchangeables simples
Les listes d’expressions interchangeables sont une bonne méthode pour optimiser la performance de votre application LUIS. Si votre application rencontre des difficultés à prédire des énoncés sur l’intention appropriée, ou à reconnaître des entités, vérifiez si les énoncés contiennent des mots inhabituels, ou des mots dont le sens peut être ambigu. Ces mots sont de bons candidats à inclure dans une liste d’expressions.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Les listes d’expressions permettent d’identifier les intentions grâce à une meilleure compréhension du contexte
Une liste d’expressions n’est pas une instruction pour que LUIS effectue une correspondance stricte ni n’étiquette exactement pareil les termes dans la liste d’expressions. Il s’agit simplement d’un conseil. Par exemple, vous pourriez avoir une liste d’expressions qui indique que « Patti » et « Selma » sont des noms, mais LUIS peut toujours utiliser des informations contextuelles pour reconnaître qu’ils signifient autre chose dans « Réserver pour 2 chez Patti’s Diner pour le dîner » et « Me trouver des itinéraires routiers pour Selma, Géorgie ». 

L’ajouter d’une liste d’expressions est une alternative à l’ajout de plusieurs exemples d’énoncés à une intention. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quand utiliser des listes d’expressions et des entités de liste
Même si une liste d’expressions et des [entités de liste](reference-entity-list.md) peuvent avoir un impact sur les énoncés dans toutes les intentions, chacune le fait d’une manière différente. Utilisez une liste d’expressions pour affecter le score de prédiction d’intention. Utilisez une entité de liste pour affecter l’extraction d’entité pour une correspondance texte exacte. 

### <a name="use-a-phrase-list"></a>Utiliser une liste d’expressions
Avec une liste d’expressions, LUIS peut toujours prendre en compte le contexte et généraliser afin d’identifier les éléments qui sont similaires, mais pas une correspondance exacte, à des éléments d’une liste. Si votre application LUIS doit être en mesure de généraliser et d’identifier les nouveaux éléments dans une catégorie, utilisez une liste d’expressions. 

Si vous souhaitez pouvoir reconnaître les nouvelles instances d’une entité, (par exemple, pour un planificateur de réunions qui doit reconnaître les noms de nouveaux contacts ou une application d’inventaire qui doit reconnaître de nouveaux produits), utilisez un autre type d’entité apprise automatiquement, comme une entité simple. Créez ensuite une liste d’expressions de mots et d’expressions qui permet à LUIS de rechercher d’autres mots similaires à l’entité. Cette liste guide LUIS afin qu’il reconnaisse des exemples de l’entité en ajoutant une précision supplémentaire à la valeur de ces mots. 

Les listes d’expression sont comme du vocabulaire spécifique à un domaine qui permet d’améliorer la qualité de compréhension des intentions et des entités. Une utilisation courante d’une liste d’expressions s’applique aux noms propres comme les noms de villes. Un nom de ville peut contenir plusieurs mots avec des traits d’union ou des apostrophes.
 
### <a name="dont-use-a-phrase-list"></a>Ne pas utiliser une liste d’expressions 
Une entité de liste définit explicitement chaque valeur qu’une entité peut prendre et n’identifie que les valeurs qui correspondent exactement. Une entité de liste peut être appropriée pour une application dans laquelle toutes les instances d’une entité sont connues et ne changent pas souvent. Les aliments dans un menu de restaurant qui change rarement en sont des exemples. Si vous avez besoin d’une correspondance texte exacte d’une entité, n’utilisez pas une liste d’expressions. 

## <a name="best-practices"></a>Bonnes pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez [Ajouter des fonctionnalités](luis-how-to-add-features.md) pour découvrir comment ajouter des fonctionnalités à votre application LUIS.
