---
title: Bot - Node.js - v3
titleSuffix: Azure Cognitive Services
description: Générez un bot intégré avec une application LUIS à l’aide de Bot Framework 3.x et du bot d’application web Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/30/2019
ms.author: diberry
ms.openlocfilehash: 27ebe827e97c8361c7f93a125f09aa9e339a6a14
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58087106"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Bot LUIS dans Node.js avec Bot Framework 3.x et bot d’application web Azure

À l’aide de Node.js, créez un bot conversationnel intégré avec compréhension de la langue (LUIS). Ce bot conversationnel utilise le domaine HomeAutomation prédéfini pour implémenter rapidement une solution de bot. Le bot est créé avec Bot Framework 3.x et le bot d’application web Azure.

## <a name="prerequisite"></a>Configuration requise

Avant de créer le bot, suivez les étapes de la section [Créer une application](./luis-get-started-create-app.md) pour générer l’application LUIS qu’il utilise.

Le bot répond aux intentions du domaine HomeAutomation qui se trouvent dans l’application LUIS. Pour chacun de ces objectifs, l’application LUIS propose une intention associée. Le bot fournit un dialogue qui gère l’intention détectée par LUIS.

| Intention | Exemple d’énoncé | Fonctionnalité du bot |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Allume les lumières. | Le bot appelle `TurnOnDialog` lorsque `HomeAutomation.TurnOn` est détecté. Dans ce dialogue, vous appelez un service IoT pour mettre un appareil sous tension et indiquer à l’utilisateur qu’il est allumé. |
| HomeAutomation.TurnOff | Éteins les lumières de la chambre. | Le bot appelle `TurnOffDialog` lorsque `HomeAutomation.TurnOff` est détecté. Dans ce dialogue, vous appelez un service IoT pour arrêter un appareil et indiquer à l’utilisateur qu’il est éteint. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Créer un bot Language Understanding avec Bot Service

1. Sur le [Portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** dans le panneau de menu, puis **Tout afficher**.

    ![Afficher toutes les ressources dans le portail Azure](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. Dans la zone de recherche, recherchez **Web App Bot**. 

    ![Sélectionner le bot d’application web pour commencer le processus de création de ressources](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. Dans le panneau **Bot Service**, indiquez les informations requises, puis sélectionnez **Créer** pour créer et déployer le service Bot Service et l’application LUIS sur Azure. Si vous souhaitez utiliser la [préparation vocale](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), consultez les [conditions régionales](troubleshooting.md#what-luis-regions-support-bot-framework-speech-priming) avant de créer votre bot. 
   * Dans **Nom de l’application**, entrez le nom de votre bot. Il sera utilisé comme sous-domaine lors du déploiement de votre bot sur le cloud (par exemple, mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
   * Sélectionnez l’abonnement, le [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), le plan App Service et [l’emplacement](https://azure.microsoft.com/regions/).
   * Pour **Modèle de bot**, sélectionnez :
       * **SDK v3**
       * **Node.JS**
       * **Language Understanding**
   * Sélectionnez **l’emplacement de l’application LUIS**. L’application sera créée dans cette [région][LUIS].
   * Cochez la case de confirmation de la mention légale. Les conditions de la mention légale figurent sous la case à cocher.

     ![Panneau Bot Service](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Vérifiez que le service Bot Service a été déployé.
    * Sélectionnez Notifications (l’icône représentant une cloche qui se trouve dans la partie supérieure du Portail Azure). La notification passera de **Déploiement lancé** à **Déploiement réussi**.
    * Sélectionnez alors **Accéder à la ressource** sur la notification **Déploiement réussi**.

## <a name="try-the-default-bot"></a>Essayer le bot par défaut

Vérifiez que le bot a été déployé en consultant les **Notifications**. La notification passera de **Déploiement en cours…** à **Déploiement réussi**. Sélectionnez le bouton **Accéder à la ressource** pour ouvrir le panneau des ressources du bot.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Installer les ressources NPM
Installez les packages NPM en suivant les étapes ci-dessous :

1. Sélectionnez **Générer** dans la section **Gestion du bot** du bot Web App. 

2. Une deuxième fenêtre de navigateur s’ouvre. Sélectionnez **Ouvrir l’éditeur de code en ligne**.

3. Dans la barre de navigation supérieure, sélectionnez le nom du bot Web App `homeautomationluisbot`. 

4. Dans la liste déroulante, sélectionnez **Ouvrir la console Kudu**.

5. Une nouvelle fenêtre de navigateur s’ouvre. Dans la console, entrez la commande suivante :

    ```console
    cd site\wwwroot && npm install
    ```

    Attendez que le processus d’installation se termine. Revenez sur la première fenêtre de navigateur. 

## <a name="test-in-web-chat"></a>Tester dans la Discussion Web
Une fois le bot enregistré, sélectionnez **Tester dans la Discussion Web** pour ouvrir le volet Discussion Web. Tapez « Bonjour » dans la Discussion Web.

  ![Tester le bot dans la Discussion Web](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Le bot répond : « Vous vous trouvez dans l’intention Salutations. Vous avez dit : "Bonjour". » Cela confirme que le bot a reçu votre message et l’a transmis à une application LUIS par défaut qu’il a créée. Celle-ci a détecté une intention de type Salutations. Dans l’étape suivante, vous allez connecter le bot à l’application LUIS que vous avez créée plutôt qu’à l’application LUIS par défaut.

## <a name="connect-your-luis-app-to-the-bot"></a>Connecter l’application LUIS au bot

Ouvrez **Paramètres de l’application** dans la première fenêtre de navigateur et ajoutez l’ID de votre application LUIS dans le champ **LuisAppId**.

  ![Mettre à jour l’ID d’application LUIS dans Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Si vous ne connaissez pas l’ID de l’application LUIS, connectez-vous au site web [LUIS](luis-reference-regions.md) avec le compte que vous utilisez pour vous connecter à Azure. Sélectionnez **Mes applications**. 

1. Localisez l’application LUIS que vous avez créée et qui contient les intentions et les entités du domaine HomeAutomation.

2. Sur la page **Paramètres** de l’application LUIS, recherchez et copiez l’ID de l’application.

3. Si vous n’avez pas encore entraîné l’application, sélectionnez le bouton **Entraîner** en haut à droite pour effectuer l'apprentissage.

4. Si vous n’avez pas encore publié l’application, sélectionnez **PUBLIER** dans la barre de navigation supérieure pour ouvrir la page **Publier**. Sélectionnez l’emplacement de production et le bouton **Publier**.

## <a name="modify-the-bot-code"></a>Modifier le code du bot

Accédez à la deuxième fenêtre de navigateur si elle est toujours ouverte ; sinon, sélectionnez **Générer**, puis **Ouvrir l’éditeur de code en ligne** dans la première fenêtre de navigateur.

   ![Ouvrir l’éditeur de code en ligne](./media/luis-tutorial-node-bot/bot-service-build.png)

Dans l’éditeur de code, ouvrez `app.js`. Le fichier comporte le code suivant :

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

Les intentions existantes dans app.js sont ignorées. Vous pouvez les laisser. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Ajouter un dialogue correspondant à HomeAutomation.TurnOn

Copiez le code suivant et ajoutez-le à `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

L’option [matches][matches] de l’action [triggerAction][triggerAction] rattachée au dialogue spécifie le nom de l’intention. Le module de reconnaissance s’exécute chaque fois que le bot reçoit un énoncé de la part de l’utilisateur. Si l’intention qui possède le meilleur score correspond à une action `triggerAction` liée à un dialogue, le bot appelle ce dialogue.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Ajouter un dialogue correspondant à HomeAutomation.TurnOff

Copiez le code suivant et ajoutez-le à `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Tester le bot

Sur le Portail Azure, sélectionnez **Tester dans la Discussion Web** pour tester le bot. Essayez de taper des messages comme « Allumer les lumières » et « Éteindre mon radiateur » pour appeler les intentions qui vous y avez ajoutées.
   ![Tester le bot HomeAutomation dans la Discussion Web](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Si vous trouvez que votre bot ne reconnaît pas toujours la bonne intention ou les bonnes entités, améliorez les performances de votre application LUIS en lui donnant d’autres exemples d’énoncés pour l’entraîner. Ce nouvel apprentissage est possible sans aucune modification du code du bot. Voir [Ajouter des exemples d’énoncés](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) et [Entraîner et tester une application LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>En savoir plus sur Bot Framework
En savoir plus sur [Bot Framework](https://dev.botframework.com/) et sur les kits de développement logiciel (SDK) [3.x](https://github.com/Microsoft/BotBuilder) et [4.x](https://github.com/Microsoft/botbuilder-js).

## <a name="next-steps"></a>Étapes suivantes

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.-->
Vous pouvez tenter d’ajouter d’autres modes, tels que Help, Annuler et message d’accueil, à l’application de LUIS. Ajoutez ensuite des dialogues pour les nouvelles intentions et testez-les avec le bot. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Ajouter des intentions](./luis-how-to-add-intents.md)
> [Préparation vocale](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches


[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

