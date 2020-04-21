---
title: LUIS et QnAMaker – Intégration de bot
titleSuffix: Azure Cognitive Services
description: À mesure que votre base de connaissances QnA Maker augmente, il devient difficile de la maintenir en tant qu’ensemble monolithique unique. Fractionnez la base de connaissances en plusieurs segments logiques plus petits.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 09/26/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0d28c77966668f919cdf1265f8cc63b4931d5fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402714"
---
# <a name="use-a-bot-with-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Utiliser un bot avec QnA Maker et LUIS pour distribuer votre base de connaissances
À mesure que votre base de connaissances QnA Maker augmente, il devient difficile de la maintenir en tant qu’ensemble monolithique unique. Fractionnez la base de connaissances en plusieurs segments logiques plus petits.

Bien qu’il soit simple de créer plusieurs bases de connaissances dans QnA Maker, vous aurez besoin d’une logique pour acheminer la question entrante vers la base de connaissances appropriée. Pour ce faire, utilisez LUIS.

Cet article utilise le Kit de développement logiciel (SDK) Bot Framework v3. Si la version v4 du Kit de développement logiciel (SDK) Bot Framework de ces informations vous intéresse, consultez [Utiliser plusieurs modèles LUIS et QnA](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-dispatch?view=azure-bot-service-4.0&tabs=csharp).

## <a name="architecture"></a>Architecture

![Graphique présentant l’architecture de QnA Maker avec Language Understanding](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Le graphique précédent montre que QnA Maker obtient d’abord l’intention de la question entrante à partir d’un modèle LUIS. Ensuite, QnA Maker utilise cette intention pour acheminer la question vers la base de connaissances QnA Maker appropriée.

## <a name="create-a-luis-app"></a>Créer une application LUIS

1. Connectez-vous au portail [LUIS](https://www.luis.ai/).
1. [Créez une application](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
1. [Ajoutez une intention](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) pour chaque base de connaissances QnA Maker. Les exemples d’énoncés doivent correspondre à des questions figurant dans les bases de connaissances QnA Maker.
1. [Effectuez l’apprentissage de l’application LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) puis [publiez l’application LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp).
1. Dans la section **Gérer**, prenez note de l’ID de votre application LUIS, de la clé du point de terminaison LUIS et du [nom de domaine personnalisé](../../cognitive-services-custom-subdomains.md). Vous aurez besoin de ces valeurs ultérieurement.

## <a name="create-qna-maker-knowledge-bases"></a>Créer des bases de connaissances QnA Maker

1. Connectez-vous à [QnA Maker](https://qnamaker.ai).
1. [Créez](https://www.qnamaker.ai/Create) une base de connaissances pour chaque intention dans l’application LUIS.
1. Testez et publiez les bases de connaissances. Lorsque vous publiez une base de connaissances, notez son ID, son nom de ressource (sous-domaine personnalisé précédant _.azurewebsites.net/qnamaker_) et la clé du point de terminaison d’autorisation. Vous aurez besoin de ces valeurs ultérieurement.

    Cet article part du principe que toutes les bases de connaissances ont été créées dans le même abonnement Azure QnA Maker.

    ![Capture d’écran de la requête HTTP QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

## <a name="web-app-bot"></a>Web App Bot

1. [Créez un bot « De base » avec Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-quickstart?view=azure-bot-service-4.0), qui comprend automatiquement une application LUIS. Sélectionnez le langage de programmation C#.

1. Une fois le bot d’application web créé, sélectionnez-le dans le portail Azure.
1. Dans la navigation du service du bot d’application web, sélectionnez **Paramètres d’application**. Faites défiler jusqu’à la section **Paramètres d’application** des paramètres disponibles.
1. Définissez **LuisAppId** sur la valeur de l’application LUIS créée dans la section précédente. Ensuite, sélectionnez **Enregistrer**.


## <a name="change-the-code-in-the-basicluisdialogcs-file"></a>Modifier le code dans le fichier BasicLuisDialog.cs
1. Dans la section **Gestion du robot** de la navigation du robot d’application web dans le portail Azure, sélectionnez **Générer**.
2. Sélectionnez **Ouvrir l’éditeur de code en ligne**. Un nouvel onglet de navigateur s’ouvre en affichant l’environnement d’édition en ligne.
3. Dans la section **WWWROOT**, sélectionnez le répertoire **Dialogues**, puis ouvrez **BasicLuisDialog.cs**.
4. Ajoutez des dépendances au début du fichier **BasicLuisDialog.cs** :

    ```csharp
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Luis;
    using Microsoft.Bot.Builder.Luis.Models;
    using Newtonsoft.Json;
    using System.Text;
    ```

5. Ajoutez les classes ci-dessous pour désérialiser la réponse de QnA Maker :

    ```csharp
    public class Metadata
    {
        public string name { get; set; }
        public string value { get; set; }
    }

    public class Answer
    {
        public IList<string> questions { get; set; }
        public string answer { get; set; }
        public double score { get; set; }
        public int id { get; set; }
        public string source { get; set; }
        public IList<object> keywords { get; set; }
        public IList<Metadata> metadata { get; set; }
    }

    public class QnAAnswer
    {
        public IList<Answer> answers { get; set; }
    }
    ```


6. Ajoutez la classe suivante pour adresser une requête HTTP au service QnA Maker. Notez que la valeur de l’en-tête **Authorization** inclut le mot `EndpointKey`, suivi d’une espace. Le résultat JSON est désérialisé dans les classes précédentes et la première réponse est retournée.

    ```csharp
    [Serializable]
    public class QnAMakerService
    {
        private string qnaServiceResourceName;
        private string knowledgeBaseId;
        private string endpointKey;

        public QnAMakerService(string resourceName, string kbId, string endpointkey)
        {
            qnaServiceResourceName = resourceName;
            knowledgeBaseId = kbId;
            endpointKey = endpointkey;

        }
        async Task<string> Post(string uri, string body)
        {
            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(body, Encoding.UTF8, "application/json");
                request.Headers.Add("Authorization", "EndpointKey " + endpointKey);

                var response = await client.SendAsync(request);
                return  await response.Content.ReadAsStringAsync();
            }
        }
        public async Task<string> GetAnswer(string question)
        {
            string uri = qnaServiceResourceName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer";
            string questionJSON = "{\"question\": \"" + question.Replace("\"","'") +  "\"}";

            var response = await Post(uri, questionJSON);

            var answers = JsonConvert.DeserializeObject<QnAAnswer>(response);
            if (answers.answers.Count > 0)
            {
                return answers.answers[0].answer;
            }
            else
            {
                return "No good match found.";
            }
        }
    }
    ```


7. Modifiez la classe `BasicLuisDialog`. Chaque intention LUIS doit avoir une méthode décorée avec **LuisIntent**. Le paramètre pour la décoration est le nom d’intention LUIS réel. Le nom de méthode décoré _doit_ être le nom d’intention LUIS pour la lisibilité et la maintenabilité, mais ne doit pas être le même au moment de la conception ou de l’exécution.

    ```csharp
    [Serializable]
    public class BasicLuisDialog : LuisDialog<object>
    {
        // LUIS Settings
        static string LUIS_appId = "<LUIS APP ID>";
        static string LUIS_apiKey = "<LUIS API KEY>";
        static string LUIS_hostRegion = "westus.api.cognitive.microsoft.com";

        // QnA Maker global settings
        // assumes all knowledge bases are created with same Azure service
        static string qnamaker_endpointKey = "<QnA Maker endpoint KEY>";
        static string qnamaker_resourceName = "my-qnamaker-s0-s";

        // QnA Maker Human Resources Knowledge base
        static string HR_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // QnA Maker Finance Knowledge base
        static string Finance_kbID = "<QnA Maker KNOWLEDGE BASE ID>";

        // Instantiate the knowledge bases
        public QnAMakerService hrQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", HR_kbID, qnamaker_endpointKey);
        public QnAMakerService financeQnAService = new QnAMakerService("https://" + qnamaker_resourceName + ".azurewebsites.net", Finance_kbID, qnamaker_endpointKey);

        public BasicLuisDialog() : base(new LuisService(new LuisModelAttribute(
            LUIS_appId,
            LUIS_apiKey,
            domain: LUIS_hostRegion)))
        {
        }

        [LuisIntent("None")]
        public async Task NoneIntent(IDialogContext context, LuisResult result)
        {
            HttpClient client = new HttpClient();
            await this.ShowLuisResult(context, result);
        }

        // HR Intent
        [LuisIntent("HR")]
        public async Task HumanResourcesIntent(IDialogContext context, LuisResult result)
        {
            // Ask the HR knowledge base
            var qnaMakerAnswer = await hrQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }

        // Finance intent
        [LuisIntent("Finance")]
        public async Task FinanceIntent(IDialogContext context, LuisResult result)
        {
            // Ask the finance knowledge base
            var qnaMakerAnswer = await financeQnAService.GetAnswer(result.Query);
            await context.PostAsync($"{qnaMakerAnswer}");
            context.Wait(MessageReceived);
        }
        private async Task ShowLuisResult(IDialogContext context, LuisResult result)
        {
            await context.PostAsync($"You have reached {result.Intents[0].Intent}. You said: {result.Query}");
            context.Wait(MessageReceived);
        }
    }
    ```


## <a name="build-the-bot"></a>Générer le bot
1. Dans l’éditeur de code, cliquez avec le bouton droit sur **build.cmd**, puis sélectionnez **Exécuter à partir de la console**.

    ![Capture d’écran de l’option Exécuter à partir de la console dans l’éditeur de code](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Le mode code est remplacé par une fenêtre de terminal indiquant la progression et les résultats de la génération.

    ![Capture d’écran de la génération de la console](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Tester le bot
Dans le portail Azure, sélectionnez **Test dans le chat web** pour tester le bot. Tapez des messages à partir d’intentions différentes pour obtenir la réponse à partir de la base de connaissances correspondante.

![Capture d’écran du test de discussion web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Intégrer votre base de connaissances avec un agent dans Power Virtual Agents](integrate-with-power-virtual-assistant-fallback-topic.md)
