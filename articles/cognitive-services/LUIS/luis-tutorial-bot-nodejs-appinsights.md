---
title: 'Didacticiel : Application Insights, Node.js - LUIS'
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
ms.openlocfilehash: b0bc4a93df7db7145accf2b485c45256f53c324d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498956"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Didacticiel : Ajouter des résultats LUIS à Application Insights à partir d’un bot dans Node.js
Ce didacticiel montre comment ajouter des informations de bot et LUIS à un stockage de données de télémétrie [Application Insights](https://azure.microsoft.com/services/application-insights/). Une fois que vous disposez de ces données, vous pouvez les interroger avec le langage de requête Kusto ou Power BI pour analyser, agréger et générer des rapports sur les intentions et les entités de l’énoncé en temps réel. Cette analyse vous aide à déterminer si vous devez ajouter ou modifier les intentions et les entités de votre application LUIS.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Capturer des données de bot et LUIS dans Application Insights
> * Interroger Application Insights pour obtenir des données LUIS

## <a name="prerequisites"></a>Prérequis

* Bot Azure Bot Service créé avec Application Insights activé.
* Code de bot téléchargé à partir du **[didacticiel](luis-nodejs-tutorial-bf-v4.md)** précédent sur les bots. 
* [Émulateur de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Tout le code utilisé dans ce didacticiel est disponible dans le [dépôt GitHub Azure-Samples Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Ajouter Application Insights à un projet de bot d’application web
Actuellement, le service Application Insights, utilisé dans ce bot d’application web, collecte une télémétrie d’état général pour le bot. Il ne collecte pas d’informations LUIS. 

Pour capturer les informations LUIS, le bot d’application web a besoin du package NPM **[Application Insights](https://www.npmjs.com/package/applicationinsights)** installé et configuré.  

1. Dans le terminal intégré de VSCode, à la racine du projet de bot, ajoutez les packages NPM suivants à l’aide de la commande indiquée : 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Le package **underscore** est utilisé pour aplatir la structure JSON de LUIS. Il est plus facile à voir et à utiliser dans Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturer et envoyer des résultats de requête LUIS à Application Insights

1. Dans VSCode, créez un fichier **appInsightsLog.js** et ajoutez le code suivant :

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Ce fichier prend le contexte du bot et la réponse LUIS, aplatit les deux objets et les insère dans un événement **Trace** dans Application Insights. Le nom de l’événement est **LUIS**. 

1. Ouvrez le dossier **folders**, puis le fichier **luisHelper.js**. Incluez le nouveau fichier **appInsightsLog.js** en tant que fichier requis, et capturez le contexte du bot et la réponse LUIS. Voici le code complet pour ce fichier : 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Ajouter une clé d’instrumentation Application Insights 

Pour ajouter des données à Application Insights, vous avez besoin de la clé d’instrumentation.

1. Dans un navigateur, sur le [portail Azure](https://portal.azure.com), recherchez la ressource **Application Insights** de votre bot. Son nom comprend la majeure partie du nom du bot et des caractères aléatoires à la fin du nom, par exemple, `luis-nodejs-bot-johnsmithxqowom`. 
1. Sur la ressource Application Insights, dans la page **Vue d’ensemble**, copiez la **Clé d’instrumentation**.
1. Dans VSCode, ouvrez le fichier **.env** à la racine du projet de bot. Ce fichier contient toutes vos variables d’environnement.  
1. Ajoutez une nouvelle variable, `MicrosoftApplicationInsightsInstrumentationKey`, avec la valeur de votre clé d’instrumentation. Ne mettez pas la valeur entre guillemets. 

## <a name="start-the-bot"></a>Démarrer le bot

1. À partir du terminal intégré VSCode, démarrez le bot :
    
    ```console
    npm start
    ```

1. Démarrer l’émulateur de bot et ouvrez le bot. Cette [étape](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) est présentée dans le didacticiel précédent.

1. Posez une question au bot. Cette [étape](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) est présentée dans le didacticiel précédent.

## <a name="view-luis-entries-in-application-insights"></a>Afficher les entrées de LUIS dans Application Insights

Ouvrez Application Insights pour voir les entrées de LUIS. L’affichage des données dans Application Insights prend quelques minutes.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights du bot. 
1. Lorsque la ressource s’ouvre, sélectionnez **Recherche**, puis recherchez dans toutes les données des dernières **30 minutes** le type d’événement **Trace**. Sélectionnez la trace nommée **LUIS**. 
1. Les informations de bot et LUIS sont disponibles sous **Propriétés personnalisées**. 

    ![Examiner les propriétés personnalisées LUIS stockées dans Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Interroger Application Insights pour les intentions, les scores et les énoncés
Application Insights vous permet d’interroger les données avec le langage [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use), ainsi que de les exporter vers [Power BI](https://powerbi.microsoft.com). 

1. Sélectionnez **Log Analytics**. Une nouvelle fenêtre s’ouvre avec une fenêtre de requête dans la partie supérieure, et une fenêtre de table de données en dessous. Si vous avez déjà utilisé des bases de données, cette disposition est courante. La requête représente vos données filtrées précédentes. La colonne **CustomDimensions** contient les informations de bot et LUIS.
1. Pour extraire l’intention première, le score et l’énoncé, ajoutez ce qui suit juste au-dessus de la dernière ligne (ligne `|top...`) dans la fenêtre de requête :

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Exécutez la requête. Les nouvelles colonnes des principaux score, intention et énoncé sont disponibles. Sélectionnez la colonne topIntent pour la trier.

Apprenez-en davantage sur le [langage de requête Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou l’[exportation des données vers Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Étapes suivantes

D’autres informations que vous pourriez vouloir ajouter aux données d’Application Insights sont l’ID d’application, l’ID de version, la dernière date de modification du modèle, la dernière date d’apprentissage et la dernière date de publication. Ces valeurs peuvent être récupérées soit à partir de l’URL de point de terminaison (ID d’application et ID de version), soit à partir d’un appel de l’API Création, puis définies dans les paramètres de bot d’application web et extraites à partir de là.  

Si vous utilisez le même abonnement de point de terminaison pour plusieurs applications LUIS, vous devez également inclure l’ID d’abonnement et une propriété indiquant qu’il s’agit d’une clé partagée. 

> [!div class="nextstepaction"]
> [En savoir plus sur les exemples d’énoncés](luis-how-to-add-example-utterances.md)
