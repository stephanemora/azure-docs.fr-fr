---
title: Exemples de bons énoncés
titleSuffix: Language Understanding - Azure Cognitive Services
description: Les énoncés sont des entrées de l’utilisateur que votre application doit interpréter. Collectez des phrases dont vous pensez que les utilisateurs les entreront. Incluez des énoncés de sens identique, mais construits différemment sur le plan de la longueur et du positionnement des mots.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: ae090a8cd812868f63c9805b2f5b59769a715090
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975268"
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

Il peut s’agir d’une phrase, telle que « Me réserver un billet pour Paris », ou d’un fragment de phrase, comme « Réservation » ou « Vol pour Paris ».  Les utilisateurs font souvent des fautes d’orthographe. Lorsque vous planifiez votre application, décidez d’utiliser ou non [Vérification orthographique Bing][(luis-tutorial-bing-spellcheck) pour corriger l’entrée de utilisateur avant de la transmettre à LUIS. 

Si vous décidez de ne pas vérifier l’orthographe des énoncés des utilisateurs, vous devez former LUIS sur des énoncés contenant des fautes de frappe et d’orthographe.

### <a name="use-the-representative-language-of-the-user"></a>Utiliser la langue représentative de l’utilisateur

Lorsque vous choisissez des énoncés, n’oubliez pas que ce que vous pensez être un terme ou une expression courants pourrait ne pas être correct pour l’utilisateur de votre application cliente. Celui-ci n’a peut-être aucune expérience dans ce domaine. Soyez prudent lorsque vous utilisez des termes ou expressions que seul un utilisateur expert utiliserait.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Choisir une terminologie et une formulation variées

Vous constaterez que, même si vous vous efforcez de créer des modèles de phrase variés, vous continuerez à répéter certains termes.

Prenez ces exemples d’énoncés :

|Exemples d’énoncés|
|--|
|Comment obtenir un ordinateur ?|
|Où obtenir un ordinateur ?|
|Je souhaite obtenir un ordinateur, comment faire ?|
|Quand puis-je avoir un ordinateur ?| 

Ici, le terme clé, « ordinateur », n’a pas de variante. Utilisez des alternatives telles que « ordinateur de bureau », « ordinateur portable », « station de travail » ou même juste « machine ». LUIS déduit intelligemment des synonymes du contexte mais, lorsque vous créez des énoncés pour l’apprentissage, il est toujours préférable de les faire varier.

## <a name="example-utterances-in-each-intent"></a>Exemples d’énoncés dans chaque intention

Chaque intention doit être associée à des exemples d’énoncés, au minimum 15. Si vous avez une intention dépourvue d’exemple d’énoncé, vous ne pouvez pas former LUIS. Si vous avez une intention avec un seul exemple d’énoncé ou très peu d’exemples, LUIS ne prédira pas l’intention avec précision. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Ajoutez de petits groupes de 15 énoncés pour chaque itération de création

Dans chaque itération du modèle, n’ajoutez pas une grande quantité d’énoncés. Ajoutez une quinzaine d’énoncés. [Formez](luis-how-to-train.md), [publiez](luis-how-to-publish-app.md) et [testez](luis-interactive-test.md) à nouveau.  

LUIS génère des modèles efficaces avec des énoncés soigneusement sélectionnés par l’auteur du modèle LUIS. L’ajout d’un trop grand nombre d’énoncés n’est pas productif, car cela introduit de la confusion.  

Il est préférable de commencer avec quelques énoncés, puis d’[examiner les énoncés de point de terminaison](luis-how-to-review-endoint-utt.md) pour vérifier le bon fonctionnement de la prédiction d’intention et de l’extraction entité.

## <a name="punctuation-marks"></a>Signes de ponctuation

Par défaut, LUIS n’ignore pas les marques de ponctuation, car certaines applications clientes peuvent leur accorder une importance. Veillez à ce que vos exemples d’énoncés soient à la fois avec et sans signe de ponctuation afin que les deux styles retournent les mêmes scores relatifs. Si la ponctuation n’a aucune signification spécifique dans votre application cliente, vous pouvez [ignorer les signes de ponctuation](#ignoring-words-and-punctuation) à l’aide de modèles. 

## <a name="ignoring-words-and-punctuation"></a>Ignorer les mots et les signes de ponctuation

Si vous souhaitez ignorer des mots ou des signes de ponctuation spécifiques dans l’exemple d’énoncé, utilisez un [modèle](luis-concept-patterns.md#pattern-syntax) avec la syntaxe _ignore_. 

## <a name="training-utterances"></a>Énoncés de formation

La formation (ou l’entraînement) n’est généralement pas déterministe : la prédiction d’énoncé peut varier légèrement selon la version ou l’application. Vous pouvez supprimer une formation non déterministe en mettant à jour l’[API des paramètres de la version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) avec la paire nom-valeur `UseAllTrainingData` afin d’utiliser toutes les données d’entraînement.

## <a name="testing-utterances"></a>Test des énoncés 

Les développeurs doivent commencer à tester leur application LUIS avec un trafic réel en envoyant des énoncés à l’URL du [point de terminaison de prédiction](luis-how-to-manage-keys.md). Ces énoncés sont utilisés pour améliorer les performances des intentions et des entités à l’aide d’un [examen des énoncés](luis-how-to-review-endoint-utt.md). Les tests soumis via le volet de test du site web LUIS ne sont pas envoyés via le point de terminaison. Ils ne contribuent donc pas à un apprentissage actif. 

## <a name="review-utterances"></a>Examen des énoncés

Une fois votre modèle formé et publié, et après réception des requêtes de [point de terminaison](luis-glossary.md#endpoint), [examinez les énoncés](luis-how-to-review-endoint-utt.md) suggérés par LUIS. LUIS sélectionne sur le point de terminaison les énoncés qui présentent des scores bas en lien avec l’intention ou l’entité. 

## <a name="best-practices"></a>Bonnes pratiques

Consultez les [meilleures pratiques](luis-concept-best-practices.md) et appliquez-les dans le cadre de votre cycle de création ordinaire.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la formation d’une application LUIS pour comprendre les énoncés des utilisateurs, voir [Ajouter des exemples d’énoncés](luis-how-to-add-example-utterances.md).

