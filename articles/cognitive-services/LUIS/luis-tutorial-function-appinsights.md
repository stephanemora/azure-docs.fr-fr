---
title: Données Application Insights issues de LUIS avec Node.js
titleSuffix: Azure Cognitive Services
description: Créer un bot intégré avec une application LUIS et Application Insights à l’aide de Node.js.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 6199e4a681f7f58ea0cf57b575afb2a63d160eee
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321952"
---
# <a name="add-luis-results-to-application-insights"></a>Ajouter des résultats de LUIS à Application Insights
Ce didacticiel montre comment ajouter des informations de demande et de réponse de LUIS à un stockage de données de télémétrie [Application Insights](https://azure.microsoft.com/services/application-insights/). Une fois que vous disposez de ces données, vous pouvez les interroger avec le langage de requête Kusto ou Power BI pour analyser, agréger et générer des rapports sur les intentions et les entités de l’énoncé en temps réel. Cette analyse vous aide à déterminer si vous devez ajouter ou modifier les intentions et les entités de votre application LUIS.

Le bot est créé avec Bot Framework 3.x et le bot d’application web Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
* Ajouter une bibliothèque Application Insights à un bot d’application web
* Capturer et envoyer des résultats de requête LUIS à Application Insights
* Interroger Application Insights pour obtenir les principaux score, intention et énoncé

## <a name="prerequisites"></a>Prérequis

* Votre bot d’application web LUIS du **[didacticiel précédent](luis-nodejs-tutorial-build-bot-framework-sample.md)** avec Application Insights activé. 

> [!Tip]
> Si vous n’avez pas encore d’abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).

Tout le code de ce didacticiel est disponible sur le [dépôt GitHub LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs), et chaque ligne associée à ce didacticiel est mise en commentaire avec `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Bot d’application web avec LUIS
Ce didacticiel suppose que vous disposez d’un code ressemblant au code suivant, ou que vous avez suivi l’[autre didacticiel](luis-nodejs-tutorial-build-bot-framework-sample.md) : 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Ajouter une bibliothèque Application Insights à un bot d’application web
Actuellement, le service Application Insights, utilisé dans ce bot d’application web, collecte une télémétrie d’état général pour le bot. Il ne collecte pas les informations de demande et de réponse de LUIS dont vous avez besoin pour vérifier et résoudre vos intentions et entités. 

Afin de capturer les demandes et réponses de LUIS, le bot d’application web a besoin du package NPM **[Application Insights](https://www.npmjs.com/package/applicationinsights)** installé et configuré dans le fichier **app.js**. Ensuite, les gestionnaires de boîte de dialogue d’intention doivent envoyer les informations de demande et de réponse de LUIS à Application Insights. 

1. Sur le portail Azure, dans le service de bot d’application web, dans la section **Bot Management** (Gestion de bot), sélectionnez **Build** (Générer). 

    ![Rechercher des informations d’application](./media/luis-tutorial-appinsights/build.png)

2. Un nouvel onglet de navigateur s’ouvre avec l’Éditeur App Service. Sélectionnez le nom de l’application dans la barre supérieure, puis sélectionnez **Open Kudu Console** (Ouvrir la console Kudu). 

    ![Rechercher des informations d’application](./media/luis-tutorial-appinsights/kudu-console.png)

3. Dans la console, entrez la commande suivante pour installer Application Insights et les packages Underscore :

    ```
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Rechercher des informations d’application](./media/luis-tutorial-appinsights/npm-install.png)

    Attendez que les packages soient installés :

    ```
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Vous avez terminé avec l’onglet de navigateur de la console Kudu.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Capturer et envoyer des résultats de requête LUIS à Application Insights
1. Dans l’onglet de navigateur de l’Éditeur App Service, ouvrez le fichier **app.js**.

2. Ajoutez les bibliothèques NPM suivantes sous les lignes `requires` existantes :

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Créez l’objet Application Insights, et utilisez le paramètre d’application de bot d’application web **BotDevInsightsKey** : 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Ajoutez la fonction **appInsightsLog** :

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    La dernière ligne de la fonction est l’emplacement où les données sont ajoutées à Application Insights. Le nom de l’événement est **LUIS-results**. Il s’agit d’un nom unique distinct de toutes les autres données de télémétrie collectées par ce bot d’application web. 

5. Utilisez la fonction **appInsightsLog**. Ajoutez-la à chaque boîte de dialogue d’intention :

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Pour tester votre bot d’application web, utilisez la fonctionnalité **Test in Web Chat** (Tester dans une conversation web). Vous ne devriez voir aucune différence, car tout le travail est effectué dans Application Insights, pas dans les réponses du bot.

## <a name="view-luis-entries-in-application-insights"></a>Afficher les entrées de LUIS dans Application Insights
Ouvrez Application Insights pour voir les entrées de LUIS. 

1. Dans le portail, sélectionnez **Toutes les ressources**, puis filtrez sur le nom du bot d’application web. Cliquez sur la ressource du type **Application Insights**. L’icône d’Application Insights est une ampoule. 

    ![Rechercher des informations d’application](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. Lorsque la ressource s’ouvre, cliquez sur l’icône **Rechercher** de la loupe dans le panneau le plus à droite. Un nouveau panneau s’affiche à droite. Selon la quantité de données de télémétrie trouvées, l’affichage du panneau peut prendre une seconde. Recherchez `LUIS-results`, puis appuyez sur Entrée. La liste est réduite aux résultats de requête de LUIS ajoutés avec ce didacticiel.

    ![Filtrer sur les dépendances](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Sélectionnez l’entrée du haut. Une nouvelle fenêtre affiche des données plus détaillées, dont les données personnalisées pour la requête de LUIS tout à fait à droite. Les données incluent la première intention et son score.

    ![Détails de la dépendance](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Lorsque vous avez terminé, sélectionnez le **X** en haut à droite pour revenir à la liste des éléments de dépendance. 


> [!Tip]
> Si vous souhaitez enregistrer la liste des dépendances et y revenir plus tard, cliquez sur **...Plus**, puis cliquez sur **Enregistrer favori**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Interroger Application Insights pour les intentions, les scores et les énoncés
Application Insights vous permet d’interroger les données avec le langage [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), ainsi que de les exporter vers [PowerBI](https://powerbi.microsoft.com). 

1. Cliquez sur **Analytics** en haut de la liste des dépendances, au-dessus de la zone de filtre. 

    ![Bouton Analytics](./media/luis-tutorial-appinsights/analytics-button.png)

2. Une nouvelle fenêtre s’ouvre avec une fenêtre de requête dans la partie supérieure, et une fenêtre de table de données en dessous. Si vous avez déjà utilisé des bases de données, cette disposition est courante. La requête inclut tous les éléments des dernières 24 heures commençant par le nom `LUIS-results`. La colonne **CustomDimensions** contient les résultats de requête de LUIS en tant que paires nom/valeur.

    ![Fenêtre de requête d’Analytics](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Pour extraire les principaux score, intention et énoncé, ajoutez ce qui suit juste au-dessus de la dernière ligne dans la fenêtre de requête :

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Exécutez la requête. Faites défiler jusqu’à l’extrême droite dans la table de données. Les nouvelles colonnes des principaux score, intention et énoncé sont disponibles. Cliquez sur la colonne des principales intentions pour la trier.

    ![Première intention d’Analytics](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


En savoir plus sur le [langage de requête Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou l’[exportation des données vers Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Étapes suivantes

D’autres informations que vous pourriez vouloir ajouter aux données d’Application Insights sont l’ID d’application, l’ID de version, la dernière date de modification du modèle, la dernière date d’apprentissage et la dernière date de publication. Ces valeurs peuvent être récupérées soit à partir de l’URL de point de terminaison (ID d’application et ID de version), soit à partir d’un appel de l’[API Création](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d), puis définies dans les paramètres de bot d’application web et extraites à partir de là.  

Si vous utilisez le même abonnement de point de terminaison pour plusieurs applications LUIS, vous devez également inclure l’ID d’abonnement et une propriété indiquant qu’il s’agit d’une clé partagée. 

> [!div class="nextstepaction"]
> [En savoir plus sur les exemples d’énoncés](luis-how-to-add-example-utterances.md)
