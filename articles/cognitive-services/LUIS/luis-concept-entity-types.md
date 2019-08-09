---
title: Types d’entités
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Les entités extraient des données à partir de l’énoncé. Les types d’entités vous donnent une extraction prévisible des données. Il existe deux types d’entités : celles apprises par la machine et celles non apprises par la machine. Il est important de savoir avec quel type d’entité vous travaillez dans les énoncés.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 628a96c4e912341226d67a7ed8f241194e7b7825
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080040"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Types d’entités et leurs objectifs dans LUIS

Les entités extraient des données à partir de l’énoncé. Les types d’entités vous donnent une extraction prévisible des données. Il existe deux types d’entités : celles apprises par la machine et celles non apprises par la machine. Il est important de savoir avec quel type d’entité vous travaillez dans les énoncés. 

## <a name="entity-compared-to-intent"></a>Comparaison entre entité et intention

L’entité représente un mot ou une phrase dans l’énoncé que vous souhaitez extraire. Un énoncé peut inclure plusieurs entités ou aucune. Une application cliente peut avoir besoin de l’entité pour effectuer sa tâche, ou l’utiliser comme guide pour plusieurs choix à présenter à l’utilisateur. 

Une entité :

* Représente une classe incluant une collection d’objets similaires (lieux, choses, personnes, événements ou concepts). 
* Décrit les informations relatives à l’intention.


Par exemple, une application Recherche d’actualités peut inclure des entités telles que « sujet », « source », « mot clé » et « date de publication », qui sont des données clés pour rechercher des informations. Dans une application de réservation de voyages, « emplacement », « date », « compagnie aérienne », « classe voyage » et « tickets » sont des informations clés pour la réservation des vols (pertinentes pour l’intention « Bookflight »).

En comparaison, l’intention représente la prédiction de l’énoncé entier. 

## <a name="entities-help-with-data-extraction-only"></a>Les entités facilitent uniquement l’extraction de données.

Vous étiquetez ou marquez des entités uniquement à des fins d’extractionde celles-ci. Cela n’aide en rien à la prédiction des intentions.

## <a name="entities-represent-data"></a>Les entités représentent les données

Les entités sont des données que vous voulez extraire à partir de l’énoncé. Il peut s’agir d’un nom, d’une date, d’un nom de produit ou d’un groupe de mots. 

|Énoncé|Entité|Données|
|--|--|--|
|Acheter 3 tickets pour New York|Nombre prédéfini<br>Location.Destination|3<br>New York|
|Acheter un ticket de New York à Londres pour le 5 mars|Location.Origin<br>Location.Destination<br>datetimeV2 prédéfini|New York<br>Londres<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Les entités sont facultatives mais fortement recommandées

Les intentions sont obligatoires mais les entités sont facultatives. Vous n’avez pas besoin créer des entités pour chaque concept dans votre application, mais uniquement pour ceux dont l’application a besoin pour effectuer une action. 

Si vos énoncés ne contiennent pas les détails dont votre bot a besoin pour continuer, il est inutile de les ajouter. Vous pouvez les ajouter ultérieurement, au fur et à mesure que votre application arrive à maturité. 

Si vous ne savez pas comment utiliser les informations, ajoutez quelques entités prédéfinies courantes, comme [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md) et [phone number](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Étiquette pour la signification du mot

Si le choix des mots ou la disposition des mots est identique, mais que la signification est différente, n’utilisez pas l’entité pour l’étiqueter. 

Dans les énoncés suivants, le mot `fair` est un homographe. Il est orthographié de la même manière, mais a une signification différente :

|Énoncé|
|--|
|Quelles sont les foires qui ont lieu dans la région de Seattle cet été ?|
|L’évaluation actuelle pour Seattle est-elle juste ?|

Si vous souhaitez qu’une entité d’événement recherche toutes les données d’événement, étiquetez le mot `fair` dans le premier énoncé, mais pas dans le second.

## <a name="entities-are-shared-across-intents"></a>Les entités sont partagées entre les intentions

Les entités sont partagées entre les intentions. Elles n’appartiennent pas à une seule intention. Les entités et les intentions peuvent être associées au niveau sémantique, mais la relation n’est pas exclusive.

Dans l’énoncé « Me réserver un ticket pour Paris », « Paris » est une entité faisant référence à un emplacement. En reconnaissant les entités mentionnées dans l’énoncé de l’utilisateur, LUIS aide votre application cliente à choisir les actions spécifiques à effectuer pour répondre à la demande de l’utilisateur.

## <a name="mark-entities-in-none-intent"></a>Marquer des entités d’une intention None

Autant que possible, toutes les intentions, notamment l’intention **None**, doivent avoir des entités marquées. Cela permet à LUIS de savoir où les entités se trouvent dans les énoncés et quels mots entourent les entités. 

## <a name="entity-status-for-predictions"></a>État de l’entité pour les prédictions

Le portail LUIS vous indique quand l’entité dans un exemple d’énoncé est différente de l’entité marquée, ou est trop proche d’une autre entité et n’est donc pas claire. Cela est indiqué par un trait de soulignement rouge dans l’exemple d’énoncé. 

Pour plus d’informations, voir [Prédictions de l’état de l’entité](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Types d’entités

LUIS offre de nombreux types d’entités. Choisissez l’entité en fonction de la façon dont les données doivent être extraites et être représentées une fois extraites.

Les entités peuvent être extraites avec le machine learning, ce qui permet à LUIS de continuer à apprendre la façon dont elles apparaissent dans l’énoncé. Les entités peuvent être extraites sans machine learning, en établissant une correspondance soit avec un texte exact, soit avec une expression régulière. Les entités dans les modèles peuvent être extraites avec une implémentation mixte. 

Une fois que l’entité extraite, ses données peuvent être représentées comme une seule unité d’informations ou combinées avec d’autres entités pour former une unité d’informations que l’application cliente peut utiliser.

|Appris par la machine|Peut marquer|Tutoriel|Exemples<br>Réponse|Type d’entité|Objectif|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Composite**](#composite-entity)|Regroupement d’entités, quel que soit le type d’entité.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Liste**](#list-entity)|Liste d’éléments et de leurs synonymes extraits avec une correspondance de texte exact.|
|Mixte||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entité dont la fin est difficile à déterminer.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Prédéfinie**](#prebuilt-entity)|Déjà entraînée pour extraire différents types de données.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Expression régulière**](#regular-expression-entity)|Utilise une expression régulière pour établir une correspondance de texte.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Simple**](#simple-entity)|Contient un concept unique dans un mot ou une expression.|

Seules les entités apprises par la machine doivent être marquées dans les exemples d’énoncés. Les entités apprises par la machine fonctionnent mieux quand elles sont testées via des [requêtes du point de terminaison](luis-concept-test.md#endpoint-testing) et [l’examen des énoncés du point de terminaison](luis-how-to-review-endoint-utt.md). 

Les entités pattern.any doivent être marquées dans les exemples de modèles [Pattern](luis-how-to-model-intent-pattern.md), et non dans les exemples d’utilisateurs d’intention. 

Les entités mixtes utilisent une combinaison de méthodes de détection d’entité.

## <a name="machine-learned-entities-use-context"></a>Les entités apprises par la machine utilisent le contexte

Les entités apprises par la machine apprennent à partir du contexte dans l’énoncé. Cela rend la variation du placement dans les exemples d’énoncés significative. 

## <a name="non-machine-learned-entities-dont-use-context"></a>Les entités non apprises par la machine n’utilisent pas le contexte

Les entités suivantes non apprises par la machine ne prennent pas le contexte de l’énoncé en compte lors de la mise en correspondance des entités : 

* [Entités prédéfinies](#prebuilt-entity)
* [Entités d’expression régulière](#regular-expression-entity)
* [Répertorier des entités](#list-entity) 

Ces entités ne nécessitent pas l’étiquetage ou l’entraînement du modèle. Une fois ajoutées ou configurées, les entités sont extraites. L’inconvénient est que ces entités sont sujettes à la surcorrespondance (si le contexte avait été pris en compte, la correspondance n’aurait pas été établie). 

Cela se produit fréquemment avec les entités de liste sur les nouveaux modèles. Vous générez et testez votre modèle avec une entité de liste, mais quand vous publiez votre modèle et recevez des requêtes du point de terminaison, vous vous rendez compte que votre modèle établit une surcorrespondance en raison du manque de contexte. 

Si vous souhaitez faire correspondre des mots ou des expressions et tenir compte du contexte, vous avez deux options. La première consiste à utiliser une entité simple associée à une liste d’expressions. La liste d’expressions ne sera pas utilisée pour la mise en correspondance, mais elle aidera à signaler les mots relativement similaires (liste interchangeable). Si vous devez avoir une correspondance exacte au lieu des variations d’une liste d’expressions, utilisez une entité de liste avec un rôle, comme décrit ci-dessous.

### <a name="context-with-non-machine-learned-entities"></a>Contexte avec entités non apprises par la machine

Si vous souhaitez que le contexte de l’énoncé soit pris en compte pour les entités non apprises par la machine, vous devez utiliser des [rôles](luis-concept-roles.md).

Si vous avez une entité non apprise par la machine, par exemple une [entité prédéfinie](#prebuilt-entity), une [entité d’expression régulière](#regular-expression-entity) ou une [entité de liste](#list-entity), qui est mise en correspondance au-delà de l’instance souhaitée, la solution peut consister à créer une entité avec deux rôles. L’un des rôles capturera ce que vous recherchez, tandis que l’autre capturera ce que vous ne recherchez pas. Les deux versions devront être étiquetées dans les exemples d’énoncés.  

## <a name="composite-entity"></a>Entité composite

Une entité composite est constituée d’autres entités (prédéfinies, simples, expressions régulières et listes). Les entités distinctes forment une entité entière. 

Cette entité convient bien lorsque les données :

* Sont associées. 
* Sont liés l’un à l’autre dans le contexte de l’énoncé.
* Utilisent divers types d’entités.
* Doivent être regroupées et traitées par l’application cliente en tant qu’unité d’informations.
* Ont divers énoncés d’utilisateur nécessitant le machine learning.

![entité composite](./media/luis-concept-entities/composite-entity.png)

[Tutoriel](luis-tutorial-composite-entity.md)<br>
[Exemple de réponse JSON pour une entité](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="list-entity"></a>Entité de liste

Les entités de liste représentent un ensemble fixe, fermé de mots associés, ainsi que leurs synonymes. LUIS ne détecte pas les valeurs supplémentaires pour les entités de liste. Utilisez la fonctionnalité **Recommander** pour trouver des suggestions de nouveaux mots à partir de la liste actuelle. S’il existe plusieurs entités de liste avec la même valeur, chaque entité est retournée dans la requête du point de terminaison. 

L’entité convient bien lorsque les données de texte :

* Sont un ensemble connu.
* Ne changent pas souvent. Si vous avez besoin de modifier la liste souvent ou si vous souhaitez que la liste se développe automatiquement, une entité simple améliorée avec une liste d’expressions est un meilleur choix. 
* L’ensemble ne dépasse pas les [limites](luis-boundaries.md) maximum de LUIS pour ce type d’entité.
* Le texte de l’énoncé est une correspondance exacte avec un synonyme ou le nom canonique. LUIS n’utilise pas la liste au-delà des correspondances de texte exactes. Une simple entité de liste ne suffit pas pour résoudre la correspondance approximative, la recherche de radicaux, de pluriels et d’autres variantes. Pour gérer les variantes, envisagez d’utiliser un [modèle](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) avec la syntaxe de texte facultative.

![entité de liste](./media/luis-concept-entities/list-entity.png)

[Tutoriel](luis-quickstart-intent-and-list-entity.md)<br>
[Exemple de réponse JSON pour une entité](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Entité Pattern.any

Pattern.any est un espace réservé à longueur variable utilisé uniquement dans le gabarit d’énoncé d’un modèle pour marquer où l’entité commence et se termine.  

L’entité convient bien quand :

* La fin de l’entité peut être confondue avec le reste du texte de l’énoncé. 
[Tutoriel](luis-tutorial-pattern.md)<br>
[Exemple de réponse JSON pour une entité](luis-concept-data-extraction.md#patternany-entity-data)

**Exemple**  
Si une application cliente recherche des livres en fonction du titre, pattern.any extrait le titre complet. Un modèle d’énoncé utilisant pattern.any pour cette recherche de livre est `Was {BookTitle} written by an American this year[?]`. 

Dans le tableau suivant, chaque ligne contient deux versions de l’énoncé. L’énoncé du haut est la manière dont LUIS voit initialement l’énoncé, où il est difficile de déterminer où le titre du livre commence et finit. L’énoncé du bas est la manière dont LUIS reconnaîtra le titre du livre quand un modèle sera en place pour l’extraction. 

|Énoncé|
|--|
|L’Homme qui prenait sa femme pour un chapeau a-t-il été écrit par un Americain cette année ?<br><br>**L’Homme qui prenait sa femme pour un chapeau** a-t-il été écrit par un Americain cette année ?|
|Nature morte avec pivert a-t-il été par un Américain cette année ?<br><br>**Nature morte avec pivert** a-t-il été par un Américain cette année ?|
|La singulière tristesse du gâteau au citron : est-il un roman écrit par un Américain cette année ?<br><br>La **singulière tristesse du gâteau au citron** est-il un roman écrit par un Américain cette année ?|
|Le Petit Prince a-t-il été écrit par un Américain cette année ?<br><br>**Le Petit Prince** a-t-il été écrit par un Américain cette année ?|
||

## <a name="prebuilt-entity"></a>Entité prédéfinie

Des entités prédéfinies sont des types intégrés qui représentent des concepts courants tels que e-mail, URL et numéro de téléphone. Les noms des entités prédéfinies sont réservés. [Toutes les entités prédéfinies](luis-prebuilt-entities.md) qui sont ajoutées à l’application sont retournées dans la requête de prédiction de point de terminaison si elles figurent dans l’énoncé. 

L’entité convient bien quand :

* Les données correspondent à un cas d’usage courant pris en charge par des entités prédéfinies pour votre culture linguistique. 

Des entités prédéfinies peuvent être ajoutées et supprimées à tout moment.

![Entité prédéfinie Number (nombre)](./media/luis-concept-entities/number-entity.png)

[Tutoriel](luis-tutorial-prebuilt-intents-entities.md)<br>
[Exemple de réponse JSON pour une entité](luis-concept-data-extraction.md#prebuilt-entity-data)

Certaines de ces entités prédéfinies dans le projet open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Si votre culture ou entité spécifique n’est pas encore prise en charge, vous pouvez contribuer au projet. 

### <a name="troubleshooting-prebuilt-entities"></a>Résolution des problèmes relatifs aux entités prédéfinies

Dans le portail LUIS, si une entité prédéfinie est balisée au lieu de votre entité personnalisée, plusieurs choix s’offrent à vous pour résoudre le problème.

Les entités prédéfinies ajoutées à l’application seront _toujours_ retournées, même si l’énoncé doit extraire les entités personnalisées pour le même texte. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Changer une entité balisée dans un exemple d’énoncé

Si l’entité prédéfinie est le même texte ou jeton que l’entité personnalisée, sélectionnez le texte dans l’exemple d’énoncé et modifiez l’énoncé balisé. 

Si l’entité prédéfinie est balisée avec plus de texte ou de jetons que votre entité personnalisée, vous disposez de deux solutions :

* [Supprimer l’exemple d’énoncé](#remove-example-utterance-to-fix-tagging)
* [Supprimer l’entité prédéfinie](#remove-prebuilt-entity-to-fix-tagging)

#### <a name="remove-example-utterance-to-fix-tagging"></a>Supprimer l’exemple d’énoncé pour corriger le balisage 

Votre premier choix consiste à supprimer l’exemple d’énoncé. 

1. Supprimez l’exemple d’énoncé.
1. Réentraînez l’application. 
1. Rajoutez uniquement le mot ou l’expression qui est l’entité, qui est marquée comme entité prédéfinie, en tant qu’exemple d’énoncé complet. Le mot ou l’expression conservera l’entité prédéfinie marquée. 
1. Sélectionnez l’entité dans l’exemple d’énoncé dans la page **Intention**, basculez vers votre entité personnalisée et effectuez un nouvel entraînement. Cela devrait empêcher LUIS de marquer ce texte exact en tant qu’entité prédéfinie dans les exemples d’énoncés qui utilisent ce texte. 
1. Rajoutez l’exemple d’énoncé d’origine entier à l’intention. L’entité personnalisée doit continuer à être marquée au lieu de l’entité prédéfinie. Si l’entité personnalisée n’est pas marquée, vous devez ajouter davantage d’exemples de ce texte dans les énoncés.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Supprimer l’entité prédéfinie pour corriger le balisage

1. Supprimez l’entité prédéfinie de l’application. 
1. Dans la page **Intention**, marquez l’entité personnalisée dans l’exemple d’énoncé.
1. Effectuez l’entraînement de l’application.
1. Rajoutez l’entité prédéfinie à l’application et effectuez un nouvel entraînement de l’application. Ce correctif part du principe que l’entité prédéfinie ne fait pas partie d’une entité composite.

## <a name="regular-expression-entity"></a>Entité d’expression régulière 

Une expression régulière est préférable à un texte d’énoncé brut. Elle ignore la casse et la variante culturelle.  La correspondance d’expression régulière est appliquée après les modifications de la vérification orthographique au niveau du caractère, et non au niveau du jeton. Si l’expression régulière est trop complexe (par exemple, si elle utilise de nombreux crochets), vous ne pouvez pas l’ajouter au modèle. Utilise une partie seulement de la bibliothèque [.Net Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions). 

L’entité convient bien quand :

* Les données sont constamment mis en forme avec toute variation également cohérente.
* L’expression régulière n’a pas besoin de plus de 2 niveaux d’imbrication. 

![Entité d’expression régulière](./media/luis-concept-entities/regex-entity.png)

[Tutoriel](luis-quickstart-intents-regex-entity.md)<br>
[Exemple de réponse JSON pour une entité](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

Les expressions régulières peuvent correspondre à plus que ce à quoi vous vous attendez. La correspondance de mots numériques comme `one` et `two` en est un exemple. L’expression régulière suivante, qui fait correspondre le nombre `one` avec d’autres nombres, en est un exemple :

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Cette expression régulière fait également correspondre les mots qui se terminent par ces nombres, comme `phone`. Pour résoudre les problèmes de ce type, veillez à ce que les correspondances des expressions régulières prennent en compte les limites des mots. L’expression régulière pour utiliser les limites des mots pour cet exemple est utilisée dans l’expression régulière suivante :

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

## <a name="simple-entity"></a>Entité simple 

Une entité simple est une entité générique qui décrit un concept unique et est apprise à partir d’un contexte appris par la machine. Les entités simples étant généralement des noms tels que des noms de société, des noms de produits ou d’autres catégories de noms, ajoutez une [liste de phrases](luis-concept-feature.md) lorsque vous utilisez une entité simple pour renforcer le signal des noms utilisés. 

L’entité convient bien quand :

* Les données ne sont mises en forme de façon cohérente, mais indiquent la même chose. 

![entité simple](./media/luis-concept-entities/simple-entity.png)

[Tutoriel](luis-quickstart-primary-and-secondary-data.md)<br/>
[Exemple de réponse pour l’entité](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>Limites de l’entité

Consultez les [limites](luis-boundaries.md#model-boundaries) pour comprendre le nombre de chaque type d’entité que vous pouvez ajouter à un modèle.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Si vous avez besoin de plus que le nombre maximal d’entités 

Vous devrez peut-être utiliser des entités composites conjointement avec des rôles d’entité.

Les entités composites représentent les parties d’un ensemble. Par exemple, une entité composite nommée PlaneTicketOrder peut avoir les entités enfants Airline, Destination, DepartureCity, DepartureDate et PlaneTicketClass.

LUIS fournit également le type d’entité de liste qui n’est pas appris par la machine, mais qui permet à votre application LUIS de spécifier une liste fixe de valeurs. Consultez les [Limites de LUIS](luis-boundaries.md) pour passer en revue les limites du type d’entité de liste. 

Si vous avez envisagé ces entités mais que vous avez besoin d’aller au-delà de la limite, contactez le support technique. Pour cela, rassemblez des informations détaillées sur votre système, accédez au site web [LUIS](luis-reference-regions.md#luis-website), puis sélectionnez **Support**. Si votre abonnement Azure comprend des services de support, contactez le [support technique Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts relatifs aux bons [énoncés](luis-concept-utterance.md). 

Consultez [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.
