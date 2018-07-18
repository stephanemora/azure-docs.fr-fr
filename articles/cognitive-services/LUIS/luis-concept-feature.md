---
title: Comprendre les fonctionnalités dans les applications LUIS dans Azure | Microsoft Docs
description: En savoir plus sur les fonctionnalités, qui permettent d’améliorer la performance d’une application LUIS. Les fonctionnalités incluent des listes d’expression et des modèles pour la reconnaissance des expressions régulières.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/18/2018
ms.author: v-geberr
ms.openlocfilehash: 416fadaf52d7a71dcaab81aab3bf6099213e5af5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35379072"
---
# <a name="phrase-list-features-in-luis"></a>Fonctionnalités de liste d’expressions dans LUIS

Dans Machine Learning, une *fonctionnalité* est une caractéristique ou un attribut distinctif de données que votre système observe. 

Ajoutez des fonctionnalités à un modèle de langage afin de fournir des conseils sur la façon de reconnaître les entrées que vous souhaitez étiqueter ou classer. Les fonctionnalités aident LUIS à reconnaître les intentions et les entités, mais les fonctionnalités ne sont pas elles-mêmes des intentions ou des entités. Les fonctionnalités peuvent toutefois fournir des exemples de termes connexes.  

## <a name="what-is-a-phrase-list-feature"></a>Qu’est-ce qu’une fonctionnalité de liste d’expressions ?
Une liste d’expressions inclut un groupe de valeurs (mots ou expressions) qui appartiennent à la même classe et qui doivent être traitées de la même façon (par exemple, des noms de villes ou des produits). Ce que LUIS apprend sur l’un d’entre elles s’applique automatiquement également aux autres. Il ne s’agit pas d’une [entité de liste](luis-concept-entity-types.md#types-of-entities) (correspondances de texte exactes) de mots correspondants.

Une liste d’expressions complète le vocabulaire du domaine d’application sous la forme d’un deuxième signal vers LUIS sur ces mots.

## <a name="how-to-use-phrase-lists"></a>Comment utiliser des listes d’expressions
Dans une application d’agent de voyage, créez une liste d’expressions nommée « Villes » qui contient les valeurs Londres, Paris et Le Caire. Si vous étiquetez l’une de ces valeurs comme une entité simple dans un [exemple d’énoncé](luis-how-to-add-example-utterances.md#add-simple-entity-label) dans une intention, LUIS apprend à reconnaître les autres. 

Une liste d’expressions peut être interchangeable ou non. Une liste d’expressions *interchangeables* concerne les valeurs qui sont des synonymes, et une liste d’expressions *non interchangeable* est destinée aux valeurs qui ne sont pas synonymes, mais qui sont similaires d’une autre manière. 

## <a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>Les listes d’expressions permettent d’identifier les entités échangeables simples
Les listes d’expressions échangeables constituent une bonne méthode pour optimiser la performance de votre application LUIS. Si votre application rencontre des difficultés à prédire des énoncés sur l’intention appropriée, ou à reconnaître des entités, vérifiez si les énoncés contiennent des mots inhabituels, ou des mots dont le sens peut être ambigu. Ces mots sont de bons candidats à inclure dans une liste d’expressions.

## <a name="phrase-lists-help-identify-intents-by-better-understanding-context"></a>Les listes d’expressions permettent d’identifier les intentions grâce à une meilleure compréhension du contexte
Utilisez des listes d’expressions pour les mots rares, propriétaires et étrangers. LUIS peut ne pas être en mesure de reconnaître des mots rares et propriétaires, ainsi que des mots étrangers (en dehors de la culture de l’application), et ceux-ci doivent donc être ajoutés à une liste d’expressions. Cette liste d’expressions doit être marquée comme non interchangeable, pour indiquer que l’ensemble de mots rares constitue une classe que LUIS doit apprendre à reconnaître, mais que ces mots ne sont ni synonymes, ni interchangeables entre eux.

Une liste d’expressions n’est pas une instruction pour que LUIS effectue une correspondance stricte ni n’étiquette exactement pareil les termes dans la liste d’expressions. Il s’agit simplement d’un conseil. Par exemple, vous pourriez avoir une liste d’expressions qui indique que « Patti » et « Selma » sont des noms, mais LUIS peut toujours utiliser des informations contextuelles pour reconnaître qu’ils signifient autre chose dans « Réserver pour 2 chez Patti’s Diner pour le dîner » et « Me donner cinq itinéraires routiers pour Selma, Géorgie ». 

L’ajouter d’une liste d’expressions est une alternative à l’ajout de plusieurs exemples d’énoncés à une intention. 

## <a name="when-to-use-phrase-lists-versus-list-entities"></a>Quand utiliser des listes d’expressions et des entités de liste
Même si une liste d’expressions et des entités de liste peuvent affecter des énoncés dans toutes les intentions, chacune le fait d’une manière différente. Utilisez une liste d’expressions pour affecter le score de prédiction d’intention. Utilisez une entité de liste pour affecter l’extraction d’entité pour une correspondance texte exacte. 

### <a name="use-a-phrase-list"></a>Utiliser une liste d’expressions
Avec une liste d’expressions, LUIS peut toujours prendre en compte le contexte et généraliser afin d’identifier les éléments qui sont similaires, mais pas une correspondance exacte, à des éléments d’une liste. Si votre application LUIS doit être en mesure de généraliser et d’identifier les nouveaux éléments dans une catégorie, utilisez une liste d’expressions. 

Lorsque vous souhaitez être en mesure de reconnaître les nouvelles instances d’une entité, comme un planificateur de réunion qui doit reconnaître les noms de nouveaux contacts, ou une application d’inventaire qui doit reconnaître de nouveaux produits, utilisez un autre type d’entité appris par ordinateur comme une entité simple ou hiérarchique. Créez ensuite une liste d’expressions de mots et d’expressions qui permet à LUIS de rechercher d’autres mots similaires à l’entité. Cette liste guide LUIS afin qu’il reconnaisse des exemples de l’entité en ajoutant une précision supplémentaire à la valeur de ces mots. 

Les listes d’expression sont comme du vocabulaire spécifique à un domaine qui permet d’améliorer la qualité de compréhension des intentions et des entités. Une utilisation courante d’une liste d’expressions s’applique aux noms propres comme les noms de villes. Un nom de ville peut contenir plusieurs mots avec des traits d’union ou des apostrophes.
 
### <a name="dont-use-a-phrase-list"></a>Ne pas utiliser une liste d’expressions 
Une entité de liste définit explicitement chaque valeur qu’une entité peut prendre et n’identifie que les valeurs qui correspondent exactement. Une entité de liste peut être appropriée pour une application dans laquelle toutes les instances d’une entité sont connues et ne changent pas souvent, comme les plats à la carte d’un restaurant qui ne changent pas fréquemment. Si vous avez besoin d’une correspondance texte exacte d’une entité, n’utilisez pas une liste d’expressions. 

## <a name="best-practices"></a>Meilleures pratiques
Découvrir les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

Consultez [Ajouter des fonctionnalités](luis-how-to-add-features.md) pour découvrir comment ajouter des fonctionnalités à votre application LUIS.