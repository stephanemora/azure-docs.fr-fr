---
title: Bot Language Understanding Node.js v4
titleSuffix: Azure Cognitive Services
description: À l’aide de Node.js, créez un bot conversationnel intégré avec compréhension de la langue (LUIS). Ce bot conversationnel utilise l’application Ressources humaines pour implémenter rapidement une solution de bot. Le bot est créé avec Bot Framework version 4 et le bot d’application web Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: a06bd5a1a061de82230e93b867ea88e333b3cc93
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442549"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Didacticiel : Utilisez un Web App Bot activé avec Language Understanding dans Node.js 

Utilisez Node.js pour créer un bot conversationnel intégré avec compréhension de la langue (LUIS). Le bot est conçu avec la ressource Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) et [Bot Framework V4](https://github.com/Microsoft/botbuilder-dotnet).

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer un bot d’application web. Ce processus crée une application LUIS pour vous.
> * Télécharger le projet de bot créé par le service de bot web
> * Démarrer le bot et l’émulateur localement sur votre ordinateur
> * Afficher les résultats d’énoncé dans le bot

## <a name="prerequisites"></a>Prérequis

* [Émulateur de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Créer une ressource bot d’application web

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.

1. Dans la zone de recherche, recherchez et sélectionnez **Bot d’application web**. Sélectionnez **Créer**.

1. Dans **Service Bot**, fournissez les informations requises :

    |Paramètre|Objectif|Paramétrage suggéré|
    |--|--|--|
    |Nom du robot|Nom de la ressource|`luis-nodejs-bot-` + `<your-name>`, par exemple, `luis-nodejs-bot-johnsmith`|
    |Abonnement|Abonnement où créer le bot.|Votre abonnement principal.
    |Groupe de ressources|Groupe logique de ressources Azure|Créer un groupe pour stocker toutes les ressources utilisées avec ce bot, et nommer le groupe `luis-nodejs-bot-resource-group`.|
    |Location|Région Azure, qui peut différer de la région de création ou de publication LUIS.|`westus`|
    |Niveau tarifaire|Utilisé pour les limites de demande de service et la facturation.|`F0` est le niveau gratuit.
    |Nom de l’application|Utilisé comme sous-domaine durant le déploiement de votre bot sur le cloud (par exemple, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, par exemple, `luis-nodejs-bot-johnsmith`|
    |Modèle de bot|Paramètres de Bot Framework : consultez le tableau suivant|
    |Emplacement de l’application LUIS|Doit être identique à la région de ressources LUIS|`westus`|
    |Plan App Service/Emplacement|Conservez la valeur fournie par défaut.|
    |Application Insights|Conservez la valeur fournie par défaut.|
    |ID d’application et mot de passe Microsoft|Conservez la valeur fournie par défaut.|

1. Dans le **modèle de bot**, sélectionnez les paramètres suivants, puis choisissez le bouton **Sélectionner** sous ceux-ci :

    |Paramètre|Objectif|Sélection|
    |--|--|--|
    |Version du SDK|Version de Bot Framework|**SDK v4**|
    |Langage du SDK|Langage de programmation de bot|**Node.JS**|
    |Bot|Type de bot|**Bot de base**|
    
1. Sélectionnez **Créer**. Le service de bot est alors créé et déployé sur Azure. Une partie de ce processus crée une application LUIS nommée `luis-nodejs-bot-XXXX`. Ce nom est basé sur le nom de l’application /Azure Bot Service.

    [![Créer un bot d’application web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

    ![Capture d’écran du Portail Azure, saisie du mot « hello »](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Ouvrez le fichier **dialogs -> LuisHelper.cs**. C’est à cet endroit qu’est envoyé à LUIS l’énoncé de l’utilisateur entré dans le bot. La réponse de LUIS est renvoyée par la méthode sous la forme d’un objet JSON **bookDetails**. Lorsque vous créez votre propre bot, vous devez également créer votre propre objet pour retourner les détails de LUIS. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
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

1. Ouvrez **dialogs -> bookingDialog.js** pour comprendre comment l’objet BookingDetails est utilisé pour gérer le flux de conversation. Les détails du voyage sont demandés au fil des étapes, puis la réservation complète est confirmée et finalement répétée à l’utilisateur. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Installer les dépendances et démarrer le code du bot dans Visual Studio

1. En VSCode, à partir du terminal intégré, installez les dépendances avec la commande `npm install`.
1. Toujours à partir du terminal intégré, démarrez le bot avec la commande `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Utiliser l’émulateur de bot pour tester le bot

1. Lancez l’émulateur de bot et sélectionnez **Ouvrir le bot**.
1. Dans la boîte de dialogue **Ouvrir le bot**, entrez l’URL de votre bot, par exemple `http://localhost:3978/api/messages`. L’itinéraire `/api/messages` est l’adresse web du bot.
1. Entrez **l’ID d’application Microsoft** et le **mot de passe d’application Microsoft**, figurant dans le fichier **.env** à la racine du code bot que vous avez téléchargé.

    Si vous le souhaitez, vous pouvez créer une nouvelle configuration de bot et copier les fichiers `MicrosoftAppId` et `MicrosoftAppPassword` du fichier **.env** dans le projet Visual Studio du bot. Le nom du fichier de configuration du bot doit être identique à celui du bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
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

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Posez une question au bot sur l’intention de réserver un vol

1. Dans l’émulateur de bot, réserver un vol en entrant l’énoncé suivant : 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    L’émulateur de bot demande la confirmation. 

1. Sélectionnez **Oui**. Le bot répond par un résumé de ses actions. 
1. Dans le journal de l’émulateur de bot, sélectionnez la ligne qui contient `Luis Trace`. Cela affiche la réponse JSON de LUIS pour l’intention et les entités de l’énoncé.

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres [exemples](https://github.com/microsoft/botframework-solutions) avec des bots conversationnels. 

> [!div class="nextstepaction"]
> [Créer une application Language Understanding avec un domaine de l’objet personnalisé](luis-quickstart-intents-only.md)