---
title: Bot - C# - v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: À l’aide de C#, créez un bot conversationnel intégré avec compréhension de la langue (LUIS). Ce bot conversationnel utilise l’application Ressources humaines pour implémenter rapidement une solution de bot. Le bot est créé avec Bot Framework version 4 et le bot d’application web Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 98c65b7adfe35c1ca80846ff7619dad69c5ba266
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219224"
---
# <a name="tutorial-luis-bot-in-c-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Tutoriel : Bot LUIS en C# avec Bot Framework 4.x et bot d'application web Azure
À l’aide de C#, vous pouvez créer un bot conversationnel intégré avec compréhension de la langue (LUIS). Ce bot utilise l’application HomeAutomation pour implémenter une solution de bot. Le bot est créé avec le [bot d’application web](https://docs.microsoft.com/azure/bot-service/) Azure et [Bot Framework version](https://github.com/Microsoft/botbuilder-js) v4.

**Ce tutoriel vous montre comment effectuer les opérations suivantes :**

> [!div class="checklist"]
> * Créer un bot d’application web. Ce processus crée une application LUIS pour vous.
> * Ajouter un domaine prédéfini au nouveau modèle LUIS
> * Télécharger le projet créé par le service de bot web
> * Démarrer le bot et l’émulateur localement sur votre ordinateur
> * Modifier le code de bot pour de nouvelles intentions LUIS
> * Afficher les résultats d’énoncé dans le bot

## <a name="prerequisites"></a>Prérequis

* [Émulateur de bot](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Créer un bot d’application web

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource**.

2. Dans la zone de recherche, recherchez et sélectionnez **Bot d’application web**. Sélectionnez **Créer**.

3. Dans **Service Bot**, fournissez les informations requises :

    |Paramètre|Objectif|Paramétrage suggéré|
    |--|--|--|
    |Nom du robot|Nom de la ressource|`luis-csharp-bot-` + `<your-name>`, par exemple, `luis-csharp-bot-johnsmith`|
    |Abonnement|Abonnement où créer le bot.|Votre abonnement principal.
    |Groupe de ressources|Groupe logique de ressources Azure|Créer un groupe pour stocker toutes les ressources utilisées avec ce bot, et nommer le groupe `luis-csharp-bot-resource-group`.|
    |Lieu|Région Azure, qui peut différer de la région de création ou de publication LUIS.|`westus`|
    |Niveau tarifaire|Utilisé pour les limites de demande de service et la facturation.|`F0` est le niveau gratuit.
    |Nom de l’application|Utilisé comme sous-domaine durant le déploiement de votre bot sur le cloud (par exemple, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, par exemple, `luis-csharp-bot-johnsmith`|
    |Modèle de bot|Paramètres de Bot Framework : consultez le tableau suivant|
    |Emplacement de l’application LUIS|Doit être identique à la région de ressources LUIS|`westus`|

4. Dans les **paramètres des modèles de bot**, sélectionnez les paramètres suivants, puis choisissez le bouton **Sélectionner** sous ceux-ci :

    |Paramètre|Objectif|Sélection|
    |--|--|--|
    |Version du SDK|Version de Bot Framework|**SDK v4**|
    |Langage du SDK|Langage de programmation de bot|**C#**|
    |Bot echo/de base|Type de bot|**Bot de base**|
    
5. Sélectionnez **Créer**. Le service de bot est alors créé et déployé sur Azure. Une partie de ce processus crée une application LUIS nommée `luis-csharp-bot-XXXX`. Ce nom est basé sur les noms de bot et d’application définis dans la section précédente.

    [ ![Créer un bot d’application web](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

6. Laissez cet onglet de navigateur ouvert. Pour toutes les étapes avec le portail LUIS, ouvrez un nouvel onglet de navigateur. Quand le nouveau service de bot est déployé, passez à la section suivante.

## <a name="add-prebuilt-domain-to-model"></a>Ajouter un domaine prédéfini au modèle
Une partie du déploiement du service de bot crée une application LUIS avec des intentions et des exemples d’énoncés. Le bot fournit un mappage d’intentions à la nouvelle application LUIS pour les intentions suivantes : 

|Intentions LUIS du bot de base|Exemple d’énoncé|
|--|--|
|Annuler|`stop`|
|Salutations|`hello`|
|Aide|`help`|
|Aucun|Quoi que ce soit en dehors du domaine de l’application.|

Ajouter l’application HomeAutomation prédéfinie au modèle pour gérer des énoncés tels que : `Turn off the living room lights`

1. Accédez au portail [LUIS](https://www.luis.ai), puis connectez-vous.
2. Dans la page **Mes applications**, sélectionnez la colonne **Date de création** pour trier par date de création des applications. Le service de bot Azure a créé une application dans la section précédente. Son nom est `luis-csharp-bot-` + `<your-name>`, suivi de 4 caractères aléatoires.
3. Ouvrez l’application et sélectionnez la section **Build** (Générer) dans le volet de navigation supérieur.
4. Dans le volet de navigation de gauche, sélectionnez **Prebuilt Domains** (Domaines prédéfinis).
5. Sélectionnez le domaine **HomeAutomation** en sélectionnant **Add domain** (Ajouter un domaine) sur sa carte.
6. Sélectionnez **Train** (Entraîner) dans le menu supérieur droit.
7. Dans le menu supérieur droit, sélectionnez **Publish** (Publier). 

    L’application créée par le service de bot Azure a maintenant de nouvelles intentions :

    |Nouvelles intentions du bot de base|Exemple d’énoncé|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Télécharger le bot d’application web 
Pour développer le code de bot d’application web, téléchargez-le et utilisez-le sur votre ordinateur local. 

1. Dans le portail Azure, toujours sur la ressource de bot d’application web, sélectionnez **Paramètres de l’application** et copiez les valeurs de **botFilePath** et **botFileSecret**. Vous devrez les ajouter ultérieurement à un fichier d’environnement. 

2. Dans le portail Azure, sélectionnez **Générer** dans la section **Gestion du bot**. 

3. Sélectionnez **Télécharger le code source du bot**. 

    [ ![Télécharger le code source du bot d’application web pour un bot de base](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Quand le code source est compressé, un message fournit un lien pour le télécharger. Sélectionnez le lien. 

5. Enregistrez le fichier zip sur votre ordinateur local et extrayez les fichiers. Ouvrez le projet. 

6. Ouvrez le fichier bot.js et recherchez `_services.LuisServices`. C’est à cet endroit qu’est envoyé à LUIS l’énoncé de l’utilisateur entré dans le bot.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    Le bot envoie l’énoncé de l’utilisateur à LUIS et obtient les résultats. La meilleure intention détermine le flux de la conversation. 


## <a name="start-the-bot"></a>Démarrer le bot
Avant de changer tout code ou paramètre, vérifiez que le bot fonctionne. 

1. Dans Visual Studio, ouvrez le fichier solution. 

2. Créez un fichier `appsettings.json` destiné à contenir les variables de bot recherchées par le code de bot :

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Définissez les valeurs des variables sur les valeurs que vous avez copiées à partir des paramètres de l’application du service de bot Azure à l’étape 1 de la section **[Télécharger le bot d’application web](#download-the-web-app-bot)**.

3. Dans Visual Studio, démarrez le bot. Une fenêtre de navigateur s’ouvre avec le site web du bot d’application web à l’adresse `http://localhost:3978/`.

## <a name="start-the-emulator"></a>Démarrer l’émulateur

1. Lancez l’émulateur de bot.

2. Dans l’émulateur de bot, sélectionnez le fichier *.bot à la racine du projet. Ce fichier `.bot` inclut le point de terminaison de l’URL du bot pour les messages :

    [ ![Émulateur de bot v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Entrez le secret de bot que vous avez copié à partir des paramètres de l’application du service de bot Azure à l’étape 1 de la section **[Télécharger le bot d’application web](#download-the-web-app-bot)**. Ainsi, l’émulateur peut accéder aux champs chiffrés dans le fichier `.bot`.

    ![Secret dans l’émulateur de bot v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. Dans l’émulateur de bot, entrez `Hello` et obtenez la réponse appropriée pour le bot de base.

    [ ![Réponse pour le bot de base dans l’émulateur](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Modifier le code de bot 

Dans le fichier `BasicBot.cs`, ajoutez du code pour gérer les nouvelles intentions. 

1. En haut du fichier, recherchez la section **Supported LUIS Intents** (Intentions LUIS prises en charge) et ajoutez des constantes pour les intentions HomeAutomation :

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Notez que le point, `.`, entre le domaine et l’intention à partir de l’application du portail LUIS est remplacé par un trait de soulignement, `_`. 

2. Recherchez la méthode **OnTurnAsync** qui reçoit la prédiction LUIS de l’énoncé. Ajoutez du code dans l’instruction switch afin de retourner la réponse LUIS pour les deux intentions HomeAutomation. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    Le bot n’ayant pas exactement la même réponse qu’une demande d’API REST de LUIS, il est important de découvrir les différences en examinant la réponse JSON. Les propriétés de texte et des intentions sont les mêmes, mais les valeurs de propriété des entités ont été modifiées. 

    ```JSON
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```



## <a name="view-results-in-bot"></a>Afficher les résultats dans le bot

1. Dans l’émulateur de bot, entrez l’énoncé : `Turn on the livingroom lights to 50%`

2. Le bot répond comme suit :

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>En savoir plus sur Bot Framework
Le service de bot Azure utilise le SDK de Bot Framework. Découvrez le SDK et Bot Framework :

* Documentation [d’Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Exemples Bot Builder](https://github.com/Microsoft/botbuilder-samples)
* [SDK Bot Builder](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Outils de Bot Builder](https://github.com/Microsoft/botbuilder-tools)

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé un service de bot Azure, copié le secret de bot et le chemin d’accès au fichier `.bot`, puis téléchargé le fichier zip du code. Vous avez ajouté le domaine HomeAutomation prédéfini à l’application LUIS créée dans le cadre du nouveau service de bot Azure, puis entraîné et republié l’application. Vous avez extrait le projet de code, créé un fichier d’environnement (`.env`) et défini le secret de bot ainsi que le chemin d’accès au fichier `.bot`. Dans le fichier bot.js, vous avez ajouté le code pour gérer les deux nouvelles intentions. Ensuite, vous avez testé le bot dans l’émulateur de bot afin de voir la réponse LUIS pour un énoncé d’une des nouvelles intentions. 

Consultez d’autres [exemples](https://github.com/Microsoft/AI) avec des bots conversationnels. 

> [!div class="nextstepaction"]
> [Générer un domaine personnalisé dans LUIS](luis-quickstart-intents-only.md)
