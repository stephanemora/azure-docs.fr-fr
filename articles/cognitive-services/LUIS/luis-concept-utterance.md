---
title: Bons exemples d’énoncés - LUIS
titleSuffix: Azure Cognitive Services
description: Les énoncés sont des entrées de l’utilisateur que votre application doit interpréter. Collectez des phrases dont vous pensez que les utilisateurs les entreront. Incluez des énoncés de sens identique, mais construits différemment sur le plan de la longueur et du positionnement des mots.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280807"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Comprendre ce que sont les bons énoncés pour votre application LUIS

Des **énoncés** sont des entrées de l’utilisateur que votre application doit interpréter. Pour que LUIS apprenne à en extraire des intentions et des entités, il est important de capturer une série d’exemples d’énoncés pour chaque intention. L’apprentissage actif, c’est-à-dire le processus d’apprentissage continu sur la base de nouveaux énoncés, est essentiel pour l’intelligence d’apprentissage automatique qu’offre LUIS.

Collectez des énoncés dont vous pensez que les utilisateurs les entreront. Incluez des énoncés qui signifient la même chose mais présentent des constructions différentes :

* Longueur de l’énoncé : court, moyen et long pour votre application cliente
* Longueur de mot et d’expression 
* Position des mots : entité au début, au milieu et à la fin de l’énoncé
* Grammaire 
* Forme plurielle
* Recherche de radical
* Choix de nom et de verbe
* Ponctuation : une bonne variété, avec usages corrects et incorrects, et aucune grammaire

## <a name="how-to-choose-varied-utterances"></a>Comment choisir les énoncés variés

Lorsque vous commencez à [ajouter des exemples d’énoncé](luis-how-to-add-example-utterances.md) à votre modèle LUIS, voici quelques principes à garder à l’esprit.

### <a name="utterances-arent-always-well-formed"></a>Les énoncés ne sont pas toujours correctement formés

Il peut s’agir d’une phrase, telle que « Me réserver un billet pour Paris », ou d’un fragment de phrase, comme « Réservation » ou « Vol pour Paris ».  Les utilisateurs font souvent des fautes d’orthographe. Lorsque vous planifiez votre application, décidez d’utiliser ou non [Vérification orthographique Bing](luis-tutorial-bing-spellcheck.md) pour corriger l’entrée de utilisateur avant de la transmettre à LUIS. 

Si vous décidez de ne pas vérifier l’orthographe des énoncés des utilisateurs, vous devez former LUIS sur des énoncés contenant des fautes de frappe et d’orthographe.

### <a name="use-the-representative-language-of-the-user"></a>Utiliser la langue représentative de l’utilisateur

Lorsque vous choisissez des énoncés, n’oubliez pas que ce que vous pensez être un terme ou une expression courants pourrait ne pas être correct pour l’utilisateur de votre application cliente. Celui-ci n’a peut-être aucune expérience dans ce domaine. Soyez prudent lorsque vous utilisez des termes ou expressions que seul un utilisateur expert utiliserait.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Choisir une terminologie et une formulation variées

Vous constaterez que, même si vous vous efforcez de créer des modèles de phrase variés, vous continuerez à répéter certains termes.

Prenez ces exemples d’énoncés :

|Exemples d’énoncés|
|--|
|how do I get a computer?|
|Where do I get a computer?|
|I want to get a computer, how do I go about it?|
|When can I have a computer?| 

Ici, le terme clé, « ordinateur », n’a pas de variante. Utilisez des alternatives telles que « ordinateur de bureau », « ordinateur portable », « station de travail » ou même juste « machine ». LUIS peut déduire intelligemment des synonymes à partir du contexte, mais, lorsque vous créez des énoncés pour l’apprentissage, il est toujours préférable de les faire varier.

## <a name="example-utterances-in-each-intent"></a>Exemples d’énoncés dans chaque intention

Chaque intention doit être associée à des exemples d’énoncés, au minimum 15. Si vous avez une intention dépourvue d’exemple d’énoncé, vous ne pouvez pas former LUIS. Si vous avez une intention avec un seul ou très peu d’exemples d’énoncés, LUIS peut ne pas prédire l’intention avec précision. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Ajoutez de petits groupes de 15 énoncés pour chaque itération de création

Dans chaque itération du modèle, n’ajoutez pas une grande quantité d’énoncés. Ajoutez une quinzaine d’énoncés. [Formez](luis-how-to-train.md), [publiez](luis-how-to-publish-app.md) et [testez](luis-interactive-test.md) à nouveau.  

LUIS génère des modèles efficaces avec des énoncés soigneusement sélectionnés par l’auteur du modèle LUIS. L’ajout d’un trop grand nombre d’énoncés n’est pas productif, car cela introduit de la confusion.

Il est préférable de commencer avec quelques énoncés, puis d’[examiner les énoncés de point de terminaison](luis-how-to-review-endpoint-utterances.md) pour vérifier le bon fonctionnement de la prédiction d’intention et de l’extraction entité.

## <a name="utterance-normalization"></a>Normalisation de l’énoncé

La normalisation de l’énoncé est le processus qui consiste à ignorer les effets de la ponctuation et des signes diacritiques au cours de la formation et de la prédiction.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Normalisation de l’énoncé pour les signes diacritiques et la ponctuation

La normalisation de l’énoncé est définie lorsque vous créez ou importez l’application, car il s’agit d’un paramètre dans le fichier JSON de l’application. Les paramètres de normalisation de l’énoncé sont désactivés par défaut. 

Les signes diacritiques sont des marques ou des signes dans le texte, par exemple : 

```
İ ı Ş Ğ ş ğ ö ü
```

Si votre application active la normalisation, les scores dans le volet **Test**, les tests par lot et les requêtes de point de terminaison changent pour tous les énoncés utilisant des signes diacritiques ou de la ponctuation.

Activez la normalisation de l’énoncé pour les signes diacritiques ou la ponctuation dans votre fichier d’application LUIS JSON avec le paramètre `settings`.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

La normalisation de la **ponctuation** signifie qu’avant la formation de vos modèles et avant la prédiction de vos requêtes de point de terminaison, la ponctuation est retirée des énoncés. 

La normalisation des **signes diacritiques** remplace les caractères avec des signes diacritiques dans les énoncés par des caractères normaux. Par exemple : `Je parle français` devient `Je parle francais`. 

La normalisation ne signifie pas que la ponctuation et les signes diacritiques ne s’affichent pas dans vos exemples d’énoncés ou de réponses de prédiction, mais ils seront ignorés pendant la formation et la prédiction.


### <a name="punctuation-marks"></a>Signes de ponctuation

La ponctuation est un jeton distinct dans LUIS. Un énoncé qui se termine par un point et un énoncé qui n’en comporte pas sont deux énoncés distincts, qui sont susceptibles d’obtenir deux prédictions différentes. 

Si la ponctuation n’est pas normalisée, LUIS n’ignore pas les marques de ponctuation, par défaut, car certaines applications clientes peuvent leur accorder une importance. Veillez à ce que vos exemples d’énoncés soient à la fois avec et sans signe de ponctuation afin que les deux styles retournent les mêmes scores relatifs. 

Veillez à ce que le modèle gère la ponctuation soit dans les exemples d’énoncés (avec ou sans ponctuation), soit dans les [modèles](luis-concept-patterns.md) où il est plus facile d’ignorer la ponctuation avec la syntaxe spéciale : `I am applying for the {Job} position[.]`

Si la ponctuation n’a aucune signification spécifique dans votre application cliente, vous pouvez [ignorer les signes de ponctuation](#utterance-normalization) en normalisant la ponctuation. 

### <a name="ignoring-words-and-punctuation"></a>Ignorer les mots et les signes de ponctuation

Si vous souhaitez ignorer des mots ou des signes de ponctuation spécifiques dans des modèles, utilisez un [modèle](luis-concept-patterns.md#pattern-syntax) avec la syntaxe _ignore_ ou des crochets, `[]`. 

## <a name="training-utterances"></a>Énoncés de formation

La formation (ou l’entraînement) n’est généralement pas déterministe : la prédiction d’énoncé peut varier légèrement selon la version ou l’application. Vous pouvez supprimer une formation non déterministe en mettant à jour l’[API des paramètres de la version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) avec la paire nom-valeur `UseAllTrainingData` afin d’utiliser toutes les données d’entraînement.

## <a name="testing-utterances"></a>Test des énoncés 

Les développeurs doivent commencer à tester leur application LUIS avec un trafic réel en envoyant des énoncés à l’URL du [point de terminaison de prédiction](luis-how-to-azure-subscription.md). Ces énoncés sont utilisés pour améliorer les performances des intentions et des entités à l’aide d’un [examen des énoncés](luis-how-to-review-endpoint-utterances.md). Les tests soumis via le volet de test du site web LUIS ne sont pas envoyés via le point de terminaison. Ils ne contribuent donc pas à un apprentissage actif. 

## <a name="review-utterances"></a>Examen des énoncés

Une fois votre modèle formé et publié, et après réception des requêtes de [point de terminaison](luis-glossary.md#endpoint), [examinez les énoncés](luis-how-to-review-endpoint-utterances.md) suggérés par LUIS. LUIS sélectionne sur le point de terminaison les énoncés qui présentent des scores bas en lien avec l’intention ou l’entité. 

## <a name="best-practices"></a>Bonnes pratiques

Consultez les [meilleures pratiques](luis-concept-best-practices.md) et appliquez-les dans le cadre de votre cycle de création ordinaire.

## <a name="label-for-word-meaning"></a>Étiquette pour la signification du mot

Si le choix des mots ou la disposition des mots est identique, mais que la signification est différente, n’utilisez pas l’entité pour l’étiqueter. 

Dans les énoncés suivants, le mot `fair` est un homographe. Il est orthographié de la même manière, mais a une signification différente :

|Énoncé|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

Si vous souhaitez qu’une entité d’événement recherche toutes les données d’événement, étiquetez le mot `fair` dans le premier énoncé, mais pas dans le second.


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la formation d’une application LUIS pour comprendre les énoncés des utilisateurs, voir [Ajouter des exemples d’énoncés](luis-how-to-add-example-utterances.md).

