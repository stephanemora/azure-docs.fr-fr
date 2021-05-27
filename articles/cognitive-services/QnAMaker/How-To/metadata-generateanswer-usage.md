---
title: Métadonnées avec l’API GenerateAnswer - QnA Maker
titleSuffix: Azure Cognitive Services
description: Avec QnA Maker, vous pouvez ajouter des métadonnées, sous la forme de paires clé-valeur, à vos paires question/réponse. Vous pouvez filtrer des résultats de requêtes d’utilisateurs et stocker des informations supplémentaires utilisables dans des conversations de suivi.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0daa44f0d901dc14541785f764d5ccf8f96fc737
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376671"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Obtenir une réponse avec l’API GenerateAnswer

Pour obtenir la réponse prédite à la question d’un utilisateur, utilisez l’API GenerateAnswer. Lorsque vous publiez une Base de connaissances, vous pouvez voir des informations sur l’utilisation de cette API dans la page **Publier**. Vous pouvez également configurer l’API pour filtrer les réponses en fonction des balises de métadonnées, et tester la Base de connaissances à partir du point de terminaison avec le paramètre de chaîne de requête de test.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obtenir des prédictions de réponse avec l’API GenerateAnswer

Utilisez l’[API GenerateAnswer](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) dans votre bot ou application pour rechercher dans votre Base de connaissances une réponse à une question d’un utilisateur et obtenir la meilleure correspondance parmi les paires question/réponse.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publier pour obtenir un point de terminaison GenerateAnswer

Après avoir publié votre Base de connaissances à partir du [portail QnA Maker](https://www.qnamaker.ai), ou à l’aide de [l’API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), vous pouvez obtenir les détails de votre point de terminaison GenerateAnswer.

Pour obtenir les détails de votre point de terminaison :
1. Connectez-vous à [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Dans **My knowledge bases** (Mes bases de connaissances), sélectionnez **View Code** (Afficher le code) sur la ligne de votre base de connaissances.
    ![Capture d’écran de My knowledge bases](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Examinez les détails de votre point de terminaison GenerateAnswer.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

    ![Capture d’écran des détails du point de terminaison](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (en préversion)](#tab/v2)

    ![Capture d’écran des détails du point de terminaison managés](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

Vous pouvez également obtenir les détails de votre point de terminaison à partir de l’onglet **Settings** (Paramètres) de votre base de connaissances.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuration de requête GenerateAnswer

Vous appelez GenerateAnswer à l’aide d’une requête HTTP POST. Pour obtenir un exemple de code qui montre comment appeler GenerateAnswer, consultez les [guides de démarrage rapide](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base).

La requête POST utilise :

* Obligatoirement des [paramètres d’URI](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Obligatoirement une propriété d’en-tête, `Authorization`, pour la sécurité
* Obligatoirement des [propriétés de corps](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

Le format de l’URL GenerateAnswer est le suivant :

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

N’oubliez pas de définir la propriété d’en-tête HTTP de `Authorization` avec une valeur de la chaîne `EndpointKey` et une espace final, puis la clé de point de terminaison figurant sur la page **Paramètres**.

Un exemple de corps JSON ressemble à ceci :

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

En savoir plus sur [rankerType](../concepts/best-practices.md#choosing-ranker-type).

Le JSON précédent a demandé uniquement des réponses à 30 % ou plus par rapport au score de seuil.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propriétés de réponse GenerateAnswer

La [réponse](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) est un objet JSON incluant toutes les informations nécessaires pour afficher la réponse et l’intersection suivante dans la conversation, si elle est disponible.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

Le JSON précédent a fourni une réponse avec un score de 38,5 %.

## <a name="match-questions-only-by-text"></a>Faire correspondre les questions uniquement, par texte

Par défaut, QnA Maker recherche dans les questions et les réponses. Pour rechercher dans les questions uniquement afin de générer une réponse, utilisez le paramètre `RankerType=QuestionOnly` dans le corps POST de la requête GenerateAnswer.

Vous pouvez rechercher dans la Base de connaissances publiée à l’aide de `isTest=false`, ou dans la Base de connaissances de test à l’aide de `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}

```
## <a name="use-qna-maker-with-a-bot-in-c"></a>Utiliser QnA Maker avec un bot en C#

Bot Framework donne accès aux propriétés de QnA Maker avec l’[API getAnswer ](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

Le JSON précédent a demandé uniquement des réponses à 30 % ou plus par rapport au score de seuil.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Utiliser QnA Maker avec un bot en Node.js

Bot Framework donne accès aux propriétés de QnA Maker avec l’[API getAnswer ](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

Le JSON précédent a demandé uniquement des réponses à 30 % ou plus par rapport au score de seuil.

## <a name="get-precise-answers-with-generateanswer-api"></a>Obtenir des réponses précises avec l’API GenerateAnswer

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (version stable)](#tab/v1)

Nous proposons la fonctionnalité de réponse précise uniquement dans la version managée de QnA Maker.

# <a name="custom-question-answering-preview-release"></a>[Réponses aux questions personnalisées (en préversion)](#tab/v2)

L’utilisateur peut activer des [réponses précises](../reference-precise-answering.md) lors de l’utilisation de la ressource Analyse de texte avec la fonctionnalité de réponse à une question personnalisée. Le paramètre answerSpanRequest doit être mis à jour pareillement.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

De la même façon, les utilisateurs peuvent choisir de désactiver les réponses précises en ne définissant pas le paramètre answerSpanRequest.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Paramètres de bot

Si vous souhaitez configurer des paramètres de réponse précise pour votre service bot, accédez à la ressource App Service pour votre bot. Ensuite, mettez à jour les configurations en ajoutant le paramètre suivant.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|Configuration de l'affichage|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Réponses précises uniquement|true|true|
|Réponses longues uniquement|false|false|
|Réponses longues et précises|true|false|

---

## <a name="common-http-errors"></a>Erreurs HTTP courantes

|Code|Explication|
|:--|--|
|2xx|Succès|
|400|Les paramètres de la requête sont incorrects, ce qui signifie que les paramètres obligatoires sont manquants, incorrects ou trop grands|
|400|Le corps de la requête est incorrect, ce qui signifie que l’objet JSON est manquant, mal formé ou trop grand|
|401|Clé non valide|
|403|Interdit : vous n’avez pas les autorisations appropriées|
|404|La base de connaissances n’existe pas|
|410|Cette API est déconseillée et n’est plus disponible.|

## <a name="next-steps"></a>Étapes suivantes

La page **Publier** fournit aussi des informations pour [générer une réponse](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) avec Postman ou cURL.

> [!div class="nextstepaction"]
> [Obtenir une analyse de votre base de connaissances](../how-to/get-analytics-knowledge-base.md)
> [!div class="nextstepaction"]
> [Score de confiance](../Concepts/confidence-score.md)
