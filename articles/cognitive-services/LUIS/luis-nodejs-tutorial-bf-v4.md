---
title: 'Tutoriel : Bot Language Understanding Node.js v4'
titleSuffix: Azure Cognitive Services
description: En utilisant Node.js, générez un chatbot intégré avec Language Understanding (LUIS) dans ce tutoriel. Ce bot conversationnel utilise l’application Ressources humaines pour implémenter rapidement une solution de bot. Le bot est créé avec Bot Framework version 4 et le bot d’application web Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 754d9d74a5d2c74a873145eaaddaaced29aa2ca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447998"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutoriel : Utilisez un Web App Bot activé avec Language Understanding dans Node.js 

Utilisez Node.js pour créer un bot conversationnel intégré avec compréhension de la langue (LUIS). Le bot est conçu avec la ressource Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) et [Bot Framework V4](https://github.com/Microsoft/botbuilder-dotnet).

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

**Dans ce tutoriel, vous allez découvrir comment :**

> [!div class="checklist"]
> * Créer un bot d’application web. Ce processus crée une application LUIS pour vous.
> * Télécharger le projet de bot créé par le service de bot web
> * Démarrer le bot et l’émulateur localement sur votre ordinateur
> * Afficher les résultats d’énoncé dans le bot

## <a name="prerequisites"></a>Conditions préalables requises

* [Émulateur de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Créer une ressource bot d’application web

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.

1. Dans la zone de recherche, recherchez et sélectionnez **Bot d’application web**. Sélectionnez **Create** (Créer).

1. Dans **Service Bot**, fournissez les informations requises :

    |Paramètre|Objectif|Paramétrage suggéré|
    |--|--|--|
    |Nom du robot|Nom de la ressource|`luis-nodejs-bot-` + `<your-name>`, par exemple, `luis-nodejs-bot-johnsmith`|
    |Subscription|Abonnement où créer le bot.|Votre abonnement principal.
    |Resource group|Groupe logique de ressources Azure|Créer un groupe pour stocker toutes les ressources utilisées avec ce bot, et nommer le groupe `luis-nodejs-bot-resource-group`.|
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
    
1. Sélectionnez **Create** (Créer). Le service de bot est alors créé et déployé sur Azure. Une partie de ce processus crée une application LUIS nommée `luis-nodejs-bot-XXXX`. Ce nom est basé sur le nom de l’application /Azure Bot Service.

    [![Créer un bot d’application web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Attendez que le service de bot soit créé avant de continuer.

## <a name="the-bot-has-a-language-understanding-model"></a>Le bot possède un modèle Language Understanding

Le processus de création du service de bot crée également une application LUIS avec des intentions et des exemples d’énoncés. Le bot fournit un mappage d’intentions à la nouvelle application LUIS pour les intentions suivantes : 

|Intentions LUIS du bot de base|Exemple d’énoncé|
|--|--|
|Réserver le vol|`Travel to Paris`|
|Annuler|`bye`|
|GetWeather|`what's the weather like?`|
|None|Quoi que ce soit en dehors du domaine de l’application.|

## <a name="test-the-bot-in-web-chat"></a>Tester le bot dans la Discussion Web

1. Dans le Portail Azure du nouveau bot, sélectionnez **Tester dans le Web Chat**. 
1. Dans la zone de texte **Tapez votre message**, entrez `Book a flight from Seattle to Berlin tomorrow`. Le bot répond avec la vérification que vous souhaitez réserver un vol. 

    ![Capture d’écran du Portail Azure, saisie du mot « hello »](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Vous pouvez utiliser la fonctionnalité de test pour tester rapidement votre bot. Pour des tests plus complets, y compris le débogage, téléchargez le code du bot et utilisez Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Télécharger le code source du bot d’application web
Pour développer le code de bot d’application web, téléchargez-le et utilisez-le sur votre ordinateur local. 

1. Dans le portail Azure, sélectionnez **Générer** dans la section **Gestion du bot**. 

1. Sélectionnez **Télécharger le code source du bot**. 

    [![Télécharger le code source du bot d’application web pour un bot de base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Lorsque la boîte de dialogue contextuelle demande **Inclure les paramètres d’application dans le fichier zip téléchargé ?** , sélectionnez **Oui**. Cela fournit les paramètres LUIS. 

1. Quand le code source est compressé, un message fournit un lien pour le télécharger. Sélectionnez le lien. 

1. Enregistrez le fichier zip sur votre ordinateur local et extrayez les fichiers. Ouvrez le projet dans avec Visual Studio 2019. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Examinez le code pour envoyer l’énoncé à LUIS et obtenir une réponse

1. Pour envoyer l’énoncé utilisateur au point de terminaison de prédiction LUIS, ouvrez le fichier **dialogs -> flightBookingRecognizer.js**. C’est à cet endroit qu’est envoyé à LUIS l’énoncé de l’utilisateur entré dans le bot. La réponse de LUIS est retournée par la méthode **executeLuisQuery**.  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **dialogs -> mainDialog** capture l’énoncé et l’envoie à executeLuisQuery dans la méthode actStep.


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Utiliser l’émulateur de bot pour tester le bot

Posez une question au bot sur l’intention de réserver un vol.

1. Lancez l’émulateur de bot et sélectionnez **Ouvrir le bot**.
1. Dans la boîte de dialogue **Ouvrir le bot**, entrez l’URL de votre bot, par exemple `http://localhost:3978/api/messages`. L’itinéraire `/api/messages` est l’adresse web du bot.
1. Entrez **l’ID d’application Microsoft** et le **mot de passe d’application Microsoft**, figurant dans le fichier **.env** à la racine du code bot que vous avez téléchargé.

1. Dans l’émulateur de bot, entrez `Book a flight from Seattle to Berlin tomorrow` et obtenez la même réponse pour le bot de base que celle que vous avez reçue dans le **Test dans le Chat Web**.

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Sélectionnez **Oui**. Le bot répond par un résumé de ses actions. 
1. Dans le journal de l’émulateur de bot, sélectionnez la ligne qui contient `Luis Trace`. Cela affiche la réponse JSON de LUIS pour l’intention et les entités de l’énoncé.

    [![Réponse pour le bot de base dans l’émulateur](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres [exemples](https://github.com/microsoft/botframework-solutions) avec des bots conversationnels. 

> [!div class="nextstepaction"]
> [Créer une application Language Understanding avec un domaine de l’objet personnalisé](luis-quickstart-intents-only.md)