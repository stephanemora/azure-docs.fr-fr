---
title: Comprendre les types d’entités dans les applications LUIS dans Azure | Microsoft Docs
description: Ajouter des entités (données clés dans le domaine de votre application) dans les applications Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/28/2018
ms.author: diberry
ms.openlocfilehash: ace4aa48d3bfce5f88bce8947ab568f0990d67fa
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226609"
---
# <a name="entities-in-luis"></a>Entités dans LUIS

Les entités sont des mots ou des phrases dans les énoncés qui sont des données clés dans le domaine de votre application.

## <a name="entity-compared-to-intent"></a>Comparaison entre entité et intention
L’entité représente un mot ou une phrase dans l’énoncé que vous souhaitez extraire. Un énoncé peut inclure plusieurs entités ou aucune. Une entité représente une classe, notamment une collection d’objets similaires (lieux, choses, personnes, événements ou concepts). Les entités décrivent des informations relatives à l’intention, et sont parfois essentiels pour que votre application effectue sa tâche. Par exemple, une application Recherche d’actualités peut inclure des entités telles que « sujet », « source », « mot clé » et « date de publication », qui sont des données clés pour rechercher des informations. Dans une application de réservation de voyages, « emplacement », « date », « compagnie aérienne », « classe voyage » et « tickets » sont des informations clés pour la réservation des vols (pertinentes pour l’intention « Bookflight »).

En comparaison, l’intention représente la prédiction de l’énoncé entier. 

## <a name="entities-represent-data"></a>Les entités représentent les données
Les entités sont des données que vous voulez extraire à partir de l’énoncé. Il peut s’agir d’un nom, d’une date, d’un nom de produit ou d’un groupe de mots. 

|Énoncé|Entité|Données|
|--|--|--|
|Acheter 3 tickets pour New York|Nombre prédéfini<br>Location.Destination|3<br>New York|
|Acheter un ticket de New York à Londres pour le 5 mars|Location.Origin<br>Location.Destination<br>datetimeV2 prédéfini|New York<br>Londres<br>5 mars 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Les entités sont facultatives mais fortement recommandées
Les intentions sont obligatoires mais les entités sont facultatives. Vous n’avez pas besoin créer des entités pour chaque concept dans votre application, mais uniquement pour les concepts nécessaires pour que l’application effectue une action. 

Si vos énoncés ne contiennent pas les détails dont votre bot a besoin pour continuer, il est inutile de les ajouter. Vous pouvez les ajouter ultérieurement, au fur et à mesure que votre application arrive à maturité. 

Si vous ne savez pas comment utiliser les informations, ajoutez quelques entités prédéfinies courantes, comme datetimeV2, ordinal, email et phone number.

## <a name="label-for-word-meaning"></a>Étiquette pour la signification du mot
Si le choix des mots ou la disposition des mots est identique, mais que la signification est différente, n’utilisez pas l’entité pour l’étiqueter. 

Dans les énoncés suivants, le mot `fair` est un homographe. Il est orthographié de la même manière, mais a une signification différente :

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Si vous souhaitez qu’une entité d’événement recherche toutes les données d’événement, étiquetez le mot `fair` dans le premier énoncé, mais pas dans le second.

## <a name="entities-are-shared-across-intents"></a>Les entités sont partagées entre les intentions
Les entités sont partagées entre les intentions. Elles n’appartiennent pas à une seule intention. Les entités et les intentions peuvent être associées au niveau sémantique, mais la relation n’est pas exclusive.

Dans l’énoncé « Me réserver un ticket pour Paris », « Paris » est une entité de type emplacement. En reconnaissant les entités qui sont mentionnées dans l’entrée utilisateur, LUIS vous permet de choisir les actions spécifiques à prendre pour répondre à une intention.

## <a name="assign-entities-in-none-intent"></a>Affecter des entités dans une intention None
Toutes les intentions, notamment l’intention **None**, doivent avoir des entités étiquetées. Cela permet à LUIS de savoir où les entités se trouvent dans les énoncés et quels mots entourent les entités. 

## <a name="types-of-entities"></a>Types d’entités
LUIS offre de nombreux types d’entités : entités prédéfinies, entités personnalisées issues de l’apprentissage automatique et entités de liste.

| NOM | Étiquette Can (Peut) | Description |
| -- |--|--|
| **Prédéfinie** <br/>[Personnalisée](#prebuilt)| |  **Définition**<br>Types intégrés qui représentent des concepts courants. <br><br>**Liste**<br/>nombre de phrases clés, ordinal, température, dimension, argent, âge, pourcentage, e-mail, URL, numéro de téléphone et phrase clé. <br><br>Les noms des entités prédéfinies sont réservés. <br><br>Toutes les entités prédéfinies qui sont ajoutées à l’application sont retournées dans la requête du [point de terminaison](luis-glossary.md#endpoint). Pour plus d’informations, consultez [Entités prédéfinies](./luis-prebuilt-entities.md). <br/><br/>[Exemple de réponse pour l’entité](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Expression régulière**<br/>[RegEx](#regex)||**Définition**<br>Expression régulière personnalisée pour le texte d’énoncé brut mis en forme. Elle ignore la casse et la variante culturelle.  <br><br>Cette entité est adaptée aux mots ou phrases mis en forme de manière cohérente avec toute variation également cohérente.<br><br>La correspondance d’expression régulière est appliquée après les modifications de la vérification orthographique. <br><br>Si l’expression régulière est trop complexe (par ex. elle utilise trop de crochets), vous ne pouvez pas ajouter l’expression au modèle. <br><br>**Exemple**<br>`kb[0-9]{6,}` correspond à kb123456.<br/><br/>[Démarrage rapide](luis-quickstart-intents-regex-entity.md)<br>[Exemple de réponse pour l’entité](luis-concept-data-extraction.md)|
| **Simple** <br/>[Issue de l’apprentissage automatique](#machine-learned) | ✔ | **Définition**<br>Une entité simple est une entité générique qui décrit un concept unique et est apprise à partir d’un contexte issu de l’apprentissage automatique. Le contexte inclue le choix des mots, l’emplacement des mots et la longueur de l’énoncé.<br/><br/>Il s’agit d’une bonne entité pour les mots ou les phrases qui ne sont pas mis en forme de manière cohérente, mais qui ont la même signification. <br/><br/>[Démarrage rapide](luis-quickstart-primary-and-secondary-data.md)<br/>[Exemple de réponse pour l’entité](luis-concept-data-extraction.md#simple-entity-data)|  
| **Liste** <br/>[Correspondance exacte](#exact-match)|| **Définition**<br>Les entités de liste représentent un ensemble fixe, fermé de mots associés, ainsi que leurs synonymes, dans votre système. <br><br>Chaque entité de liste peut avoir une ou plusieurs formes. Les entités de liste sont adaptées à un ensemble connu de variations sur les manières de représenter le même concept.<br/><br/>LUIS ne détecte pas les valeurs supplémentaires pour les entités de liste. Utilisez la fonctionnalité **Recommander** pour trouver des suggestions de nouveaux mots à partir de la liste actuelle.<br/><br>S’il existe plusieurs entités de liste avec la même valeur, chaque entité est retournée dans la requête du point de terminaison. <br/><br/>[Démarrage rapide](luis-quickstart-intent-and-list-entity.md)<br>[Exemple de réponse pour l’entité](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Mixte](#mixed) | ✔|**Définition**<br>Patterns.any est un espace réservé à longueur variable utilisé uniquement dans le gabarit d’énoncé d’un modèle pour marquer où l’entité commence et se termine.  <br><br>**Exemple**<br>Si un énoncé recherche des livres en fonction du titre, pattern.any extrait le titre complet. Un gabarit d’énoncé utilisant pattern.any est `Who wrote {BookTitle}[?]`.<br/><br/>[Didacticiel](luis-tutorial-pattern.md)<br>[Exemple de réponse pour l’entité](luis-concept-data-extraction.md#composite-entity-data)|  
| **Composite** <br/>[Issue de l’apprentissage automatique](#machine-learned) | ✔|**Définition**<br>Une entité composite est constituée d’autres entités (prédéfinies, simples, regex, liste ou hiérarchiques). Les entités distinctes forment une entité entière. Les entités de liste ne sont pas autorisées dans les entités composites. <br><br>**Exemple**<br>Une entité composite nommée PlaneTicketOrder peut avoir les entités enfants prédéfinies `number` et `ToLocation`. <br/><br/>[Didacticiel](luis-tutorial-composite-entity.md)<br>[Exemple de réponse pour l’entité](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hiérarchique** <br/>[Issue de l’apprentissage automatique](#machine-learned) |✔ | **Définition**<br>Une entité hiérarchique est une catégorie d’entités simples apprises de façon contextuelle.<br><br>**Exemple**<br>Avec une entité hiérarchique `Location` avec les enfants `ToLocation` et `FromLocation`, chaque enfant peut être déterminé en fonction du **contexte** dans l’énoncé. Dans l’énoncé, `Book 2 tickets from Seattle to New York`, `ToLocation` et `FromLocation` sont différents au niveau du contexte selon les mots qui les entourent. <br/><br/>**Ne pas utiliser si**<br>Si vous recherchez une entité avec des correspondances de texte exactes dans les enfants, quel que soit le contexte, vous devez utiliser une entité de liste. Si vous recherchez une relation parent-enfant avec d’autres types d’entités, vous devez utiliser l’entité Composite.<br/><br/>[Démarrage rapide](luis-quickstart-intent-and-hier-entity.md)<br>[Exemple de réponse pour l’entité](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Les entités prédéfinies** sont des entités personnalisées fournies par LUIS. Certaines de ces entités sont définies dans le projet [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) open source. Il existe de nombreux [exemples](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) dans le répertoire /Specs pour les cultures prises en charge. Si votre culture ou entité spécifique n’est pas encore prise en charge, vous pouvez contribuer au projet. 

<a name="machine-learned"></a>
**Les entités issues de l’apprentissage automatique** fonctionnent mieux quand elles sont testées via des [requêtes du point de terminaison](luis-concept-test.md#endpoint-testing) et [l’examen des énoncés du point de terminaison](luis-how-to-review-endoint-utt.md). 

<a name="regex"></a>
**Les entités d’expression régulière** sont définies par une expression régulière fournie par l’utilisateur dans le cadre de la définition de l’entité. 

<a name="exact-match"></a>
**Les entités de correspondance exacte** utilisent le texte fourni dans l’entité pour établir une correspondance exacte de texte.

<a name="mixed"></a>
**Les entités mixtes** utilisent une combinaison des méthodes de détection d’entité.

## <a name="entity-limits"></a>Limites de l’entité
Consultez les [limites](luis-boundaries.md#model-boundaries) pour comprendre le nombre de chaque type d’entité que vous pouvez ajouter à un modèle.

## <a name="entity-roles"></a>Rôles d’entité
Les [rôles](luis-concept-roles.md) d’entité ne sont utilisés que dans les modèles. 

## <a name="composite-vs-hierarchical-entities"></a>Entités composites et hiérarchiques
Les entités composites et les entités hiérarchiques ont des relations parent-enfant et sont issues de l’apprentissage automatique. L’apprentissage automatique permet à LUIS de comprendre les entités dans différents contextes (organisation des mots). Les entités composite sont plus flexibles, car elles acceptent différents types d’entités en tant qu’enfants. Les enfants d’une entité hiérarchique sont des entités simples. 

|type|Objectif|Exemples|
|--|--|--|
|Hiérarchique|Parent-enfant d’entités simples|Location.Origin=New York<br>Location.Destination=London|
|Composite|Entités parent-enfant : prédéfinie, liste, simple, hiérarchique| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Données correspondant à plusieurs entités
Si un mot ou une phrase correspond à plusieurs entités, la requête du point de terminaison retourne chaque entité. Si vous ajoutez l’entité Number prédéfinie et l’entité datetimeV2 prédéfinie, et que vous avez un énoncé `create meeting on 2018/03/12 for lunch with wayne`, LUIS reconnaît toutes les entités et retourne un tableau d’entités dans le cadre de la réponse du point de terminaison JSON : 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Données correspondant à plusieurs entités Liste
Si un mot ou une phrase correspond à plusieurs entités Liste, la requête du point de terminaison retourne chaque entité Liste.

Pour la requête `when is the best time to go to red rock?`, et si l’application contient le mot `red` dans plusieurs listes, LUIS reconnaît toutes les entités et retourne un tableau d’entités dans le cadre de la réponse du point de terminaison JSON : 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Si vous avez besoin de plus que le nombre maximal d’entités 

Vous devrez peut-être utiliser des entités hiérarchiques et composites. Les entités hiérarchiques reflètent la relation entre des entités qui partagent des caractéristiques ou qui sont membres d’une catégorie. Les entités enfants sont toutes membres de la catégorie de leur parent. Par exemple, une entité hiérarchique nommée PlaneTicketClass peut avoir les entités enfants EconomyClass et FirstClass. La hiérarchie s’étend sur un seul niveau de profondeur.  

Les entités composites représentent les parties d’un ensemble. Par exemple, une entité composite nommée PlaneTicketOrder peut avoir les entités enfants Airline, Destination, DepartureCity, DepartureDate et PlaneTicketClass. Vous générez une entité composite à partir d’entités simples existantes, d’enfants d’entités hiérarchiques ou d’entités prédéfinies.  

LUIS fournit également le type d’entité de liste qui n’est pas issu de l’apprentissage automatique, mais qui permet à votre application LUIS de spécifier une liste fixe de valeurs. Consultez les [Limites de LUIS](luis-boundaries.md) pour passer en revue les limites du type d’entité de liste. 

Si vous avez envisagé les entités hiérarchiques, composites et liste, mais avez besoin d’aller au-delà de la limite, contactez le support technique. Pour cela, rassemblez des informations détaillées sur votre système, accédez au site web [LUIS](luis-reference-regions.md#luis-website), puis sélectionnez **Support**. Si votre abonnement Azure comprend des services de support, contactez le [support technique Azure](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Meilleures pratiques

Créez une [entité](luis-concept-entity-types.md) lorsque l’application appelante ou le bot a besoin de paramètres ou de données à partir de l’énoncé requis pour exécuter une action. Une entité est un mot ou une phrase dans l’énoncé que vous avez besoin d’extraire, peut-être en tant que paramètre d’une fonction. 

Afin de sélectionner le type correct d’entité à ajouter à votre application, vous devez savoir comment les données sont entrées par les utilisateurs. Chaque entité est trouvée à l’aide d’un mécanisme différent, comme l’apprentissage automatique, une liste fermée ou une expression régulière. Si vous avez des doutes, commencez avec une entité simple et étiquetez le mot ou la phrase qui représente ces données dans tous les énoncés, pour toutes les intentions, notamment l’intention None.  

Passez en revue les énoncés du point de terminaison de manière régulière pour rechercher une utilisation commune où une entité peut être identifiée en tant qu’expression régulière ou trouvée à l’aide d’une correspondance exacte de texte.  

Dans le cadre de cet examen, envisagez d’ajouter une liste de phrases pour ajouter un signal dans LUIS pour les mots ou les phrases qui sont pertinents pour votre domaine, mais qui ne sont pas des correspondances exactes, et pour lesquels le degré de confiance dans LUIS est faible.  

Pour plus d’informations, consultez les [meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts relatifs aux bons [énoncés](luis-concept-utterance.md). 

Consultez [Ajouter des entités](luis-how-to-add-entities.md) pour découvrir comment ajouter des entités à votre application LUIS.