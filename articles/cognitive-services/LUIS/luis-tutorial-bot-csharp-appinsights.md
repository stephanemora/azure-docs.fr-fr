---
title: 'Tutoriel : Application Insights, C# - LUIS'
titleSuffix: Azure Cognitive Services
description: Ce didacticiel montre comment ajouter des informations de bot et LUIS à un stockage de données de télémétrie Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b9c47685253e2a70c7b5e947debaac6f5f3264b2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888292"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-c"></a>Tutoriel : Ajouter des résultats LUIS à Application Insights à partir d’un bot en C#

Ce didacticiel montre comment ajouter des informations de bot et LUIS à un stockage de données de télémétrie [Application Insights](https://azure.microsoft.com/services/application-insights/). Une fois que vous disposez de ces données, vous pouvez les interroger avec le langage de requête Kusto ou Power BI pour analyser, agréger et générer des rapports sur les intentions et les entités de l’énoncé en temps réel. Cette analyse vous aide à déterminer si vous devez ajouter ou modifier les intentions et les entités de votre application LUIS.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Capturer des données de bot et LUIS dans Application Insights
> * Interroger Application Insights pour obtenir des données LUIS

## <a name="prerequisites"></a>Conditions préalables requises

* Bot Azure Bot Service créé avec Application Insights activé.
* Code de bot téléchargé à partir du **[didacticiel](luis-csharp-tutorial-bf-v4.md)** précédent sur les bots. 
* [Émulateur de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Tout le code utilisé dans ce didacticiel est disponible dans le [dépôt GitHub Azure-Samples Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Ajouter Application Insights à un projet de bot d’application web

Actuellement, le service Application Insights, utilisé dans ce bot d’application web, collecte une télémétrie d’état général pour le bot. Il ne collecte pas d’informations LUIS. 

Pour capturer les informations LUIS, le bot d’application web a besoin que le package NuGet **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** soit installé et configuré.  

1. À partir de Visual Studio, ajoutez la dépendance à la solution. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Gérer les packages NuGet…** . Le Gestionnaire de packages NuGet affiche la liste des packages installés. 
1. Sélectionnez **Parcourir**, puis recherchez **Microsoft.ApplicationInsights**.
1. Installez le package. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturer et envoyer des résultats de requête LUIS à Application Insights

1. Ouvrez le fichier `LuisHelper.cs` et remplacez son contenu par le code suivant. La méthode **LogToApplicationInsights** capture les données de bot et LUIS, et les envoie à Application Insights en tant qu’événement Trace nommé `LUIS`.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Ajouter une clé d’instrumentation Application Insights 

Pour ajouter des données à Application Insights, vous avez besoin de la clé d’instrumentation.

1. Dans un navigateur, sur le [portail Azure](https://portal.azure.com), recherchez la ressource **Application Insights** de votre bot. Son nom comprend la majeure partie du nom du bot et des caractères aléatoires à la fin du nom, par exemple, `luis-csharp-bot-johnsmithxqowom`. 
1. Sur la ressource Application Insights, dans la page **Vue d’ensemble**, copiez la **Clé d’instrumentation**.
1. Dans Visual Studio, ouvrez le fichier **appsettings.json** à la racine du projet de bot. Ce fichier contient toutes vos variables d’environnement.
1. Ajoutez une nouvelle variable, `BotDevAppInsightsKey`, avec la valeur de votre clé d’instrumentation. La valeur doit être entre guillemets. 

## <a name="build-and-start-the-bot"></a>Créer et démarrer le bot

1. Dans Visual Studio, créez et exécutez le bot. 
1. Démarrer l’émulateur de bot et ouvrez le bot. Cette [étape](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) est présentée dans le didacticiel précédent.

1. Posez une question au bot. Cette [étape](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) est présentée dans le didacticiel précédent.

## <a name="view-luis-entries-in-application-insights"></a>Afficher les entrées de LUIS dans Application Insights

Ouvrez Application Insights pour voir les entrées de LUIS. L’affichage des données dans Application Insights prend quelques minutes.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights du bot. 
1. Lorsque la ressource s’ouvre, sélectionnez **Recherche**, puis recherchez dans toutes les données des dernières **30 minutes** le type d’événement **Trace**. Sélectionnez la trace nommée **LUIS**. 
1. Les informations de bot et LUIS sont disponibles sous **Propriétés personnalisées**. 

    ![Examiner les propriétés personnalisées LUIS stockées dans Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Interroger Application Insights pour les intentions, les scores et les énoncés
Application Insights vous permet d’interroger les données avec le langage [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use), ainsi que de les exporter vers [Power BI](https://powerbi.microsoft.com). 

1. Sélectionnez **Log Analytics**. Une nouvelle fenêtre s’ouvre avec une fenêtre de requête dans la partie supérieure, et une fenêtre de table de données en dessous. Si vous avez déjà utilisé des bases de données, cette disposition est courante. La requête représente vos données filtrées précédentes. La colonne **CustomDimensions** contient les informations de bot et LUIS.
1. Pour extraire l’intention première, le score et l’énoncé, ajoutez ce qui suit juste au-dessus de la dernière ligne (ligne `|top...`) dans la fenêtre de requête :

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Exécute la requête. Les nouvelles colonnes des principaux score, intention et énoncé sont disponibles. Sélectionnez la colonne topIntent pour la trier.

Apprenez-en davantage sur le [langage de requête Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou l’[exportation des données vers Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>En savoir plus sur Bot Framework

En savoir plus sur [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Étapes suivantes

D’autres informations que vous pourriez vouloir ajouter aux données d’Application Insights sont l’ID d’application, l’ID de version, la dernière date de modification du modèle, la dernière date d’apprentissage et la dernière date de publication. Ces valeurs peuvent être récupérées soit à partir de l’URL de point de terminaison (ID d’application et ID de version), soit à partir d’un appel de l’API Création, puis définies dans les paramètres de bot d’application web et extraites à partir de là.  

Si vous utilisez le même abonnement de point de terminaison pour plusieurs applications LUIS, vous devez également inclure l’ID d’abonnement et une propriété indiquant qu’il s’agit d’une clé partagée.

> [!div class="nextstepaction"]
> [En savoir plus sur les exemples d’énoncés](luis-how-to-add-example-utterances.md)
