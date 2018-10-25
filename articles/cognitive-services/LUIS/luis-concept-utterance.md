---
title: Énoncés dans les applications LUIS
titleSuffix: Azure Cognitive Services
description: Les énoncés sont des entrées de l’utilisateur que votre application doit interpréter. Collectez des phrases dont vous pensez que les utilisateurs les entreront. Incluez des énoncés de sens identique, mais construits différemment sur le plan de la longueur et du positionnement des mots.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 2b21102a5416c1fd7e2abb35be677c48ffc63263
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638069"
---
# <a name="utterances-in-luis"></a>Énoncés dans LUIS

Des **énoncés** sont des entrées de l’utilisateur que votre application doit interpréter. Pour que LUIS apprenne à en extraire des intentions et des entités, il est important de capturer une série d’entrées différentes pour chaque intention. L’apprentissage actif, c’est-à-dire le processus d’apprentissage continu sur la base de nouveaux énoncés, est essentiel pour l’intelligence d’apprentissage automatique qu’offre LUIS.

Collectez des phrases dont vous pensez que les utilisateurs les entreront. Incluez des énoncés de sens identique, mais construits différemment sur le plan de la longueur et du positionnement des mots. 

## <a name="how-to-choose-varied-utterances"></a>Comment choisir les énoncés variés
Lorsque vous commencez à [ajouter des exemples d’énoncé](luis-how-to-add-example-utterances.md) à votre modèle LUIS, voici quelques principes à garder à l’esprit.

### <a name="utterances-arent-always-well-formed"></a>Les énoncés ne sont pas toujours correctement formés
Il peut s’agir d’une phrase, telle que « Réserver un billet pour Paris », ou d’un fragment de phrase, tel que « Réservation » ou « Vol pour Paris ».  Les utilisateurs font souvent des fautes d’orthographe. Lorsque vous planifiez votre application, considérez s’il est opportun d’appliquer une vérification orthographique aux entrées des utilisateurs avant de transmettre celles-ci à LUIS. L’[API Vérification orthographique Bing][BingSpellCheck] s’intègre avec LUIS. Lorsque vous publiez votre application LUIS, vous pouvez l’associer à une clé externe pour l’API Vérification orthographique Bing. Si vous décidez de ne pas vérifier l’orthographe des énoncés des utilisateurs, vous devez former LUIS sur des énoncés contenant des fautes de frappe et d’orthographe.

### <a name="use-the-representative-language-of-the-user"></a>Utiliser la langue représentative de l’utilisateur
Lorsque vous choisissez des énoncés, n’oubliez pas que ce que vous pensez être un terme ou une expression courants pourrait ne pas l’être pour l’utilisateur de votre application cliente. Celui-ci n’a peut-être aucune expérience dans ce domaine. Par conséquent, soyez prudent lorsque vous utilisez des termes ou des expressions que seul un utilisateur expert utiliserait.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Choisir une terminologie et une formulation variées
Vous constaterez que, même si vous vous efforcez de créer des modèles de phrase variés, vous continuerez à répéter certains termes.

Prenez ces exemples d’énoncés :

|Exemples d’énoncés|
|--|
|Comment obtenir un ordinateur ?|
|Où obtenir un ordinateur ?|
|Je souhaite obtenir un ordinateur, comment faire ?|
|Quand puis-je avoir un ordinateur ?| 

Ici, le terme clé, « computer » (ordinateur), n’est pas varié. Il peut désigner un ordinateur de bureau, un ordinateur portable, une station de travail, voir une simple machine. LUIS déduit intelligemment des synonymes du contexte mais, lorsque vous créez des énoncés pour l’apprentissage, il est toujours préférable de les faire varier.

## <a name="example-utterances-in-each-intent"></a>Exemples d’énoncés dans chaque intention
Chaque intention doit avoir des exemples d’énoncés, au minimum entre 10 et 15. Si vous avez une intention dépourvue d’exemple d’énoncé, vous ne pouvez pas former LUIS. Si vous avez une intention avec un seul exemple d’énoncé ou très peu d’exemples, LUIS ne prédira pas l’intention avec précision. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Ajouter de petits groupes de 10 à 15 énoncés pour chaque itération de création
Dans chaque itération du modèle, n’ajoutez pas une grande quantité d’énoncés. Ajoutez une dizaine d’énoncés. [Formez](luis-how-to-train.md), [publiez](luis-how-to-publish-app.md) et [testez](luis-interactive-test.md) à nouveau.  

LUIS génère des modèles efficaces avec des énoncés choisis avec soin. L’ajout d’un trop grand nombre d’énoncés n’est pas productif, car cela introduit de la confusion.  

Il est préférable de commencer avec quelques énoncés, puis d’[examiner les énoncés de point de terminaison](luis-how-to-review-endoint-utt.md) pour vérifier le bon fonctionnement de la prédiction d’intention et de l’extraction entité.

## <a name="ignoring-words-and-punctuation"></a>Ignorer les mots et les signes de ponctuation
Si vous souhaitez ignorer des mots ou des signes de ponctuation spécifiques dans l’exemple d’énoncé, utilisez un [modèle](luis-concept-patterns.md#pattern-syntax) avec la syntaxe _ignore_. 

## <a name="training-utterances"></a>Énoncés de formation
La formation (ou l’apprentissage) n’est pas déterministe : la prédiction d’énoncé peut varier légèrement selon la version ou l’application.

## <a name="testing-utterances"></a>Test des énoncés 

Les développeurs doivent commencer à tester leur application LUIS avec un trafic réel en envoyant des énoncés au point de terminaison. Ces énoncés sont utilisés pour améliorer les performances des intentions et des entités à l’aide d’un [examen des énoncés](luis-how-to-review-endoint-utt.md). Les tests soumis via le volet de test du site web LUIS ne sont pas envoyés via le point de terminaison. Ils ne contribuent donc pas à un apprentissage actif. 

## <a name="review-utterances"></a>Examen des énoncés
Une fois votre modèle formé et publié, et après réception des requêtes de [point de terminaison](luis-glossary.md#endpoint), [examinez les énoncés](luis-how-to-review-endoint-utt.md) suggérés par LUIS. LUIS sélectionne sur le point de terminaison les énoncés qui présentent des scores bas en lien avec l’intention ou l’entité. 

## <a name="best-practices"></a>Meilleures pratiques
Pour en savoir plus, voir [Meilleures pratiques](luis-concept-best-practices.md).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la formation d’une application LUIS pour comprendre les énoncés des utilisateurs, voir [Ajouter des exemples d’énoncés](luis-how-to-add-example-utterances.md).

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text