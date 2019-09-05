---
title: Bot Language Understanding C# v4
titleSuffix: Azure Cognitive Services
description: À l’aide de C#, créez un bot conversationnel intégré avec compréhension de la langue (LUIS). Le bot est créé avec Bot Framework version 4 et le service du bot d’application web Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: c78359920ebc5faab2e0a678a901bcb8581a4e45
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207327"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Didacticiel : Utiliser un Web App Bot activé avec Language Understanding dans C#

À l’aide de C#, créez un bot conversationnel intégré avec compréhension de la langue (LUIS). Le bot est conçu avec la ressource Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) et [Bot Framework V4](https://github.com/Microsoft/botbuilder-dotnet).

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer un bot d’application web. Ce processus crée une application LUIS pour vous.
> * Télécharger le projet de bot créé par le service de bot web
> * Démarrer le bot et l’émulateur localement sur votre ordinateur
> * Afficher les résultats d’énoncé dans le bot

## <a name="prerequisites"></a>Prérequis

* [Émulateur de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Créer une ressource bot d’application web

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.

1. Dans la zone de recherche, recherchez et sélectionnez **Bot d’application web**. Sélectionnez **Create** (Créer).

1. Dans **Service Bot**, fournissez les informations requises :

    |Paramètre|Objectif|Paramétrage suggéré|
    |--|--|--|
    |Nom du robot|Nom de la ressource|`luis-csharp-bot-` + `<your-name>`, par exemple, `luis-csharp-bot-johnsmith`|
    |Subscription|Abonnement où créer le bot.|Votre abonnement principal.
    |Resource group|Groupe logique de ressources Azure|Créer un groupe pour stocker toutes les ressources utilisées avec ce bot, et nommer le groupe `luis-csharp-bot-resource-group`.|
    |Location|Région Azure, qui peut différer de la région de création ou de publication LUIS.|`westus`|
    |Niveau tarifaire|Utilisé pour les limites de demande de service et la facturation.|`F0` est le niveau gratuit.
    |Nom de l’application|Utilisé comme sous-domaine durant le déploiement de votre bot sur le cloud (par exemple, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, par exemple, `luis-csharp-bot-johnsmith`|
    |Modèle de bot|Paramètres de Bot Framework : consultez le tableau suivant|
    |Emplacement de l’application LUIS|Doit être identique à la région de ressources LUIS|`westus`|
    |Plan App Service/Emplacement|Conservez la valeur fournie par défaut.|
    |Application Insights|Conservez la valeur fournie par défaut.|
    |ID d’application et mot de passe Microsoft|Conservez la valeur fournie par défaut.|

1. Dans le **modèle de bot**, sélectionnez les paramètres suivants, puis choisissez le bouton **Sélectionner** sous ceux-ci :

    |Paramètre|Objectif|Sélection|
    |--|--|--|
    |Version du SDK|Version de Bot Framework|**SDK v4**|
    |Langage du SDK|Langage de programmation de bot|**C#**|
    |Bot|Type de bot|**Bot de base**|
    
1. Sélectionnez **Create** (Créer). Le service de bot est alors créé et déployé sur Azure. Une partie de ce processus crée une application LUIS nommée `luis-csharp-bot-XXXX`. Ce nom est basé sur le nom de l’application /Azure Bot Service.

    [![Créer un bot d’application web](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Attendez que le service de bot soit créé avant de continuer.

## <a name="the-bot-has-a-language-understanding-model"></a>Le bot possède un modèle Language Understanding

Le processus de création du service de bot crée également une application LUIS avec des intentions et des exemples d’énoncés. Le bot fournit un mappage d’intentions à la nouvelle application LUIS pour les intentions suivantes : 

|Intentions LUIS du bot de base|Exemple d’énoncé|
|--|--|
|Réserver le vol|`Travel to Paris`|
|Annuler|`bye`|
|Aucun|Quoi que ce soit en dehors du domaine de l’application.|

## <a name="test-the-bot-in-web-chat"></a>Tester le bot dans la Discussion Web

1. Dans le Portail Azure du nouveau bot, sélectionnez **Tester dans le Web Chat**. 
1. Dans la zone de texte **Tapez votre message**, entrez `hello`. Le bot répond avec des informations sur le framework de bot, ainsi que des exemples de requêtes pour le modèle LUIS spécifique, tel que la réservation d’un vol pour Paris. 

    ![Capture d’écran du Portail Azure, saisie du mot « hello »](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    Vous pouvez utiliser la fonctionnalité de test pour tester rapidement votre bot. Pour des tests plus complets, y compris le débogage, téléchargez le code du bot et utilisez Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Télécharger le code source du bot d’application web
Pour développer le code de bot d’application web, téléchargez-le et utilisez-le sur votre ordinateur local. 

1. Dans le portail Azure, sélectionnez **Générer** dans la section **Gestion du bot**. 

1. Sélectionnez **Télécharger le code source du bot**. 

    [![Télécharger le code source du bot d’application web pour un bot de base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Lorsque la boîte de dialogue contextuelle demande **Inclure les paramètres d’application dans le fichier zip téléchargé ?** , sélectionnez **Oui**.

1. Quand le code source est compressé, un message fournit un lien pour le télécharger. Sélectionnez le lien. 

1. Enregistrez le fichier zip sur votre ordinateur local et extrayez les fichiers. Ouvrez le projet dans avec Visual Studio 2019. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Examinez le code pour envoyer l’énoncé à LUIS et obtenir une réponse

1. Ouvrez le fichier **LuisHelper.cs**. C’est à cet endroit qu’est envoyé à LUIS l’énoncé de l’utilisateur entré dans le bot. La réponse de LUIS est renvoyée par la méthode sous la forme d’un objet **BookDetails**. Lorsque vous créez votre propre bot, vous devez également créer votre propre objet pour retourner les détails de LUIS. 


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
        }
    }
    ```

1. Ouvrez **BookingDetails.cs** pour voir comment l’objet extrait les informations LUIS. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Ouvrez **Dialogs -> BookingDialog.cs** pour comprendre comment l’objet BookingDetails est utilisé pour gérer le flux de conversation. Les détails du voyage sont demandés au fil des étapes, puis la réservation complète est confirmée et finalement répétée à l’utilisateur. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Démarrer le code du bot dans Visual Studio

Dans Visual Studio, démarrez le bot. Une fenêtre de navigateur s’ouvre avec le site web du bot d’application web à l’adresse `http://localhost:3978/`. Une page d’accueil s’affiche avec des informations sur votre bot.

![Une page d’accueil s’affiche avec des informations sur votre bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Utiliser l’émulateur de bot pour tester le bot

1. Lancez l’émulateur de bot et sélectionnez **Ouvrir le bot**.
1. Dans la boîte de dialogue **Ouvrir le bot**, entrez l’URL de votre bot, par exemple `http://localhost:3978/api/messages`. L’itinéraire `/api/messages` est l’adresse web du bot.
1. Entrez **l’ID d’application Microsoft** et le **mot de passe d’application Microsoft**, figurant dans le fichier **appsettings.json** à la racine du code bot que vous avez téléchargé.

    Si vous le souhaitez, vous pouvez créer une nouvelle configuration de bot et copier les fichiers `appId` et `appPassword` du fichier **appsettings.json** dans le projet Visual Studio du bot. Le nom du fichier de configuration du bot doit être identique à celui du bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. Dans l’émulateur de bot, entrez `Hello` et obtenez la même réponse pour le bot de base que celle que vous avez reçue dans le **Test dans le Chat Web**.

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Posez une question au bot sur l’intention de réserver un vol

1. Dans l’émulateur de bot, réserver un vol en entrant l’énoncé suivant : 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    L’émulateur de bot demande la confirmation. 

1. Sélectionnez **Oui**. Le bot répond par un résumé de ses actions. 
1. Dans le journal de l’émulateur de bot, sélectionnez la ligne qui contient `Luis Trace`. Cela affiche la réponse JSON de LUIS pour l’intention et les entités de l’énoncé.

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres [exemples](https://github.com/microsoft/botframework-solutions) avec des bots conversationnels. 

> [!div class="nextstepaction"]
> [Créer une application Language Understanding avec un domaine de l’objet personnalisé](luis-quickstart-intents-only.md)
