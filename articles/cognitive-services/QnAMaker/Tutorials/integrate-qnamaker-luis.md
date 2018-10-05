---
title: LUIS et QnAMaker - Intégration de bot
titleSuffix: Azure Cognitive Services
description: Tutoriel détaillé sur l’intégration de QnA Maker et LUIS dans un bot
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 53e46fa84bcd7b96403dcb0ec70b45b800bc4acb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042004"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Intégrer QnA Maker et LUIS pour distribuer votre base de connaissances
Au fur et à mesure que la taille de votre base de connaissances QnA Maker augmente, il devient difficile de la gérer comme un ensemble monolithique unique et il est nécessaire de fractionner la base de connaissances en parties logiques plus petites.

Même s’il est simple de créer plusieurs bases de connaissances dans QnA Maker, vous aurez besoin d’une logique pour acheminer la question entrante à la base de connaissances appropriée. Pour ce faire, utilisez LUIS.

## <a name="architecture"></a>Architecture

![Architecture LUIS de QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

Dans le scénario ci-dessus, QnA Maker obtient d’abord l’intention de la question entrante à partir d’un modèle LUIS, puis l’utilise pour l’acheminer vers la base de connaissances QnA Maker appropriée.

## <a name="prerequisites"></a>Prérequis
- Connectez-vous au portail [LUIS](https://www.luis.ai/) et [créez une application](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
- [Ajouter des intentions](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) conformément à votre scénario.
- [Formez](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) et [publiez](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) votre application LUIS.
- Connectez-vous à [QnA Maker](https://qnamaker.ai) et [créez](https://www.qnamaker.ai/Create) les bases de connaissances conformément à votre scénario.
- Testez et publiez les bases de connaissances.

## <a name="qna-maker--luis-bot"></a>Bot QnA Maker + LUIS
1. Tout d’abord, créez un bot d’application web avec le modèle LUIS, liez-le à l’application LUIS que vous avez créée précédemment et modifiez les intentions. Consultez la procédure détaillée [ici](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Ajoutez des dépendances au début du fichier, avec les autres dépendances :

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Ajoutez la classe ci-dessous pour appeler votre service QnA Maker :

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
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
        /* END - QnA Maker Response Class */
    ```

3. Accédez à https://qnamaker.ai -> Mes bases de connaissances -> Afficher le code, pour votre base de connaissances correspondante. Obtenez les informations suivantes :

    ![Requête HTTP QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Instanciez la classe QnAMakerService pour chacune de vos bases de connaissances :
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Appelez la base de connaissances correspondante pour l’intention.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
            }
    ```

## <a name="build-the-bot"></a>Générer le bot
1. Dans l’éditeur de code, cliquez avec le bouton droit sur `build.cmd` et sélectionnez **Exécuter à partir de la console**.

    ![exécuter à partir de la console](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Le mode code est remplacé par une fenêtre de terminal indiquant la progression et les résultats de la build.

    ![génération de la console](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Tester le bot
Dans le portail Azure, sélectionnez **Test dans le chat web** pour tester le bot. Tapez des messages à partir d’intentions différentes pour obtenir la réponse à partir de la base de connaissances correspondante.

![test chat web](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un plan de continuité des activités pour QnA Maker](../How-To/business-continuity-plan.md)
