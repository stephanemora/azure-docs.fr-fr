---
title: 'Tutoriel : Bot Language Understanding Node.js v4'
description: En utilisant Node.js, générez un chatbot intégré avec Language Understanding (LUIS) dans ce tutoriel. Ce bot conversationnel utilise l’application Ressources humaines pour implémenter rapidement une solution de bot. Le bot est créé avec Bot Framework version 4 et le bot d’application web Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987836"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Tutoriel : Utilisez un Web App Bot activé avec Language Understanding dans Node.js

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

1. Dans la zone de recherche, recherchez et sélectionnez **Bot d’application web**. Sélectionnez **Create** (Créer).

1. Dans **Service Bot**, fournissez les informations requises :

    |Paramètre|Objectif|Paramétrage suggéré|
    |--|--|--|
    |Nom du robot|Nom de la ressource|`luis-nodejs-bot-` + `<your-name>`, par exemple, `luis-nodejs-bot-johnsmith`|
    |Abonnement|Abonnement où créer le bot.|Votre abonnement principal.
    |Resource group|Groupe logique de ressources Azure|Créer un groupe pour stocker toutes les ressources utilisées avec ce bot, et nommer le groupe `luis-nodejs-bot-resource-group`.|
    |Emplacement|Région Azure, qui peut différer de la région de création ou de publication LUIS.|`westus`|
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

    > [!div class="mx-imgBorder"]
    > [![Créer un bot d’application web](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
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