---
title: Application Insights à l’aide de C#
titleSuffix: Azure Cognitive Services
description: Générez un bot intégré à une application LUIS et Application Insights à l’aide de C#.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 46065a742afd08585707f4f4fdf6ad2e32cd89b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719453"
---
# <a name="add-luis-results-to-application-insights-with-a-bot-in-c"></a>Ajouter des résultats LUIS à Application Insights avec un bot en C#

Ce didacticiel permet d’ajouter des informations de réponse LUIS au stockage des données de télémétrie [Application Insights](https://azure.microsoft.com/services/application-insights/). Une fois que vous avez les données, vous pouvez les interroger avec le langage Kusto ou PowerBI pour les analyser, les agréger et générer des rapports sur les intentions et les entités de l’énoncé en temps réel. Cette analyse vous aide à déterminer si vous devez ajouter ou modifier les intentions et les entités de votre application LUIS.

Le bot est créé avec Bot Framework 3.x et le bot d’application web Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter Application Insights à un bot d’application web
> * Capturer et envoyer des résultats de requête LUIS à Application Insights
> * Interroger Application Insights pour obtenir les principaux score, intention et énoncé

## <a name="prerequisites"></a>Prérequis

* Votre bot d’application web LUIS du **[didacticiel précédent](luis-csharp-tutorial-build-bot-framework-sample.md)** avec Application Insights activé.
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) installé localement sur votre ordinateur.

> [!Tip]
> Si vous n’avez pas encore d’abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).

Tout le code de ce tutoriel est disponible sur le [dépôt GitHub Azure-Samples](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp), et chaque ligne associée à ce tutoriel est mise en commentaire avec `//LUIS Tutorial:`.

## <a name="review-luis-web-app-bot"></a>Passer en revue le bot d’application web LUIS

Ce didacticiel suppose que vous disposez de code semblable à ce qui suit, ou que vous avez suivi l’[autre didacticiel](luis-csharp-tutorial-build-bot-framework-sample.md) :

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights dans un bot d’application web

Actuellement, le service Application Insights, ajouté dans le cadre de la création du service web de bot d’application web, collecte la télémétrie d’état général pour le bot. Il ne collecte pas les informations de réponse LUIS. Pour analyser et améliorer LUIS, vous avez besoin des informations de réponse LUIS.  

Pour capturer la réponse LUIS, le bot d’application web a besoin qu’**[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** soit installé et configuré pour le projet.

## <a name="download-web-app-bot"></a>Télécharger le bot d’application web

Utilisez [Visual Studio 2017](https://www.visualstudio.com/downloads/) afin d’ajouter et de configurer Application Insights pour le bot d’application web. Pour pouvoir utiliser le bot d’application web dans Visual Studio, téléchargez le code du bot d’application web.

1. Dans le portail Azure, pour le bot d’application web, sélectionnez **Générer**.

    ![Sélectionner Générer dans le portail](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Sélectionnez **Télécharger le fichier ZIP** et attendez que le fichier soit prêt.

    ![Télécharger le fichier ZIP](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Sélectionnez **Télécharger le fichier ZIP** dans la fenêtre contextuelle. N’oubliez pas l’emplacement sur votre ordinateur ; vous en aurez besoin à la section suivante.

    ![Fenêtre contextuelle pour le téléchargement du fichier ZIP](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Ouvrir la solution dans Visual Studio 2017

1. Extrayez le fichier dans un dossier.

2. Ouvrez Visual Studio 2017, puis le fichier de solution, `Microsoft.Bot.Sample.LuisBot.sln`. Si l’avertissement de sécurité s’affiche, sélectionnez « OK ».

    ![Ouvrir la solution dans Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio doit ajouter des dépendances à la solution. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Références**, puis sélectionnez **Gérer les packages NuGet**.

    ![Manage NuGet packages](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. Le Gestionnaire de packages NuGet affiche la liste des packages installés. Sélectionnez **Restaurer** dans la barre jaune. Attendez que le processus de restauration se termine.

    ![Restaurer des packages NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Ajouter Application Insights au projet

Installez et configurez Application Insights dans Visual Studio.

1. Dans Visual Studio 2017, dans le menu supérieur, sélectionnez **Projet**, puis **Ajouter Application Insights Telemetry...**

2. Dans la fenêtre **Configuration d’Application Insights**, sélectionnez **Démarrer gratuitement**.

    ![Commencer à configurer Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Inscrivez votre application auprès d’Application Insights. Vous devrez peut-être entrer vos informations d’identification de portail Azure.

4. Visual Studio ajoute Application Insights au projet, tout en affichant l’état.

    ![État d’Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Une fois le processus terminé, la fenêtre **Configuration d’Application Insights** affiche l’état d’avancement.

    ![État d’avancement d’Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    L’option **Activer la collecte des traces** est affichée en rouge, ce qui signifie qu’elle n’est pas activée. Ce didacticiel n’utilise pas cette fonctionnalité.

## <a name="build-and-resolve-errors"></a>Générer et résoudre les erreurs

1. Générez la solution en sélectionnant le menu **Générer**, puis choisissez **Régénérer la solution**. Attendez la fin de la génération.

2. Si la génération échoue avec des erreurs `CS0104`, vous devez les corriger. Dans le dossier `Controllers` (fichier `MessagesController.cs file`), corrigez l’utilisation ambiguë du type `Activity` en faisant précéder le type d’activité par le type de connecteur. Pour ce faire, changez le nom `Activity` sur les lignes 22 et 36 : remplacez la valeur `Activity` par `Connector.Activity`. Regénérez la solution. Il ne doit plus y avoir aucune erreur de génération.

    La source complète de ce fichier est :

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publier le projet sur Azure

Le package **Application Insights** est désormais dans le projet et configuré correctement vos informations d’identification dans le portail Azure. Les modifications apportées au projet doivent être publiées dans Azure.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Publier**.

    ![Publier le projet sur le portail](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. Dans la fenêtre **Publier**, sélectionnez **Créer un profil**.

    ![Dans le cadre de la publication, créez un profil.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Sélectionnez **Importer le profil**, puis **OK**.

    ![Dans le cadre de la publication, importez un profil.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. Dans la fenêtre **Importer le fichier de paramètres de publication**, accédez au dossier du projet, puis au dossier `PostDeployScripts`. Sélectionnez le fichier qui se termine par `.PublishSettings`, puis choisissez `Open`. Vous avez maintenant configuré la publication pour ce projet.

5. Publiez votre code source local vers le service de bot via le bouton **Publier**. La fenêtre **Sortie** affiche l’état. Le reste de ce didacticiel est à compléter dans le portail Azure. Fermez Visual Studio 2017.

## <a name="open-three-browser-tabs"></a>Ouvrir trois onglets de navigateur

Dans le portail Azure, recherchez le bot d’application web et ouvrez-le. La procédure suivante utilise trois vues différentes du bot d’application web. Il peut être plus facile d’ouvrir trois onglets distincts dans le navigateur :
  
>  * Test dans le chat web
>  * Générer/Ouvrir l’éditeur de code en ligne -> Éditeur App Service
>  * Éditeur App Service/Ouvrir la console Kudu -> Console de diagnostic

## <a name="modify-basicluisdialogcs-code"></a>Modifier le code de BasicLuisDialog.cs

1. Dans l’onglet de navigateur **Éditeur App Service**, ouvrez le fichier `BasicLuisDialog.cs`.

2. Ajoutez les dépendances NuGet suivantes sous les lignes `using` existantes :

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Ajoutez la fonction `LogToApplicationInsights` :

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    La clé d’instrumentation Application Insights est déjà dans le paramètre d’application `BotDevInsightsKey` du bot d’application web.

    La dernière ligne de la fonction ajoute les données à Application Insights. Le nom de la trace est `LUIS`, un nom unique parmi toutes les données de télémétrie collectées par ce bot d’application web. Toutes les propriétés sont également précédées de `LUIS_`, afin que vous sachiez quelles données ce didacticiel ajoute par rapport aux informations qui sont fournies par le bot d’application web.

4. Appelez la fonction `LogToApplicationInsights` par dessus la fonction `ShowLuisResult` :

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Générer le bot d’application web

1. Générez le bot d’application web de l’une des manières suivantes. La première méthode consiste à cliquer avec le bouton droit sur `build.cmd` dans l’**Éditeur App Service**, puis à **lancer l’exécution à partir de la console**. La sortie de la console affiche et se termine avec le message `Finished successfully.`.

2. En cas d’erreur, vous devez ouvrir la console, accéder au script et l’exécuter en procédant comme suit. Dans l’**Éditeur App Service**, sur la barre bleue supérieure, sélectionnez le nom de votre bot, puis choisissez l’option permettant d’**ouvrir la console Kudu** dans la liste déroulante.

    ![Ouvrir la console Kudu](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. Dans la fenêtre de la console, saisissez la commande suivante :

    ```console
    cd site\wwwroot && build.cmd
    ```

    Attendez la fin de la génération, annoncée par `Finished successfully.`.

## <a name="test-the-web-app-bot"></a>Tester le bot d’application web

1. Pour tester votre bot d’application web, ouvrez la fonctionnalité de **test dans le chat web** dans le portail.

2. Saisissez l’expression `Coffee bar on please`.  

    ![Tester le bot d’application web dans le chat](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Vous ne devriez voir aucune différence dans la réponse du chatbot. La modification envoie des données à Application Insights, pas dans les réponses du bot. Saisissez quelques autres énoncés pour ajouter un peu plus de données dans Application Insights :

|Énoncés|
|--|
|Please deliver a pizza|
|Turn off all the lights|
|Turn on the hall light|


## <a name="view-luis-entries-in-application-insights"></a>Afficher les entrées de LUIS dans Application Insights

Ouvrez Application Insights pour voir les entrées de LUIS.

1. Dans le portail, sélectionnez **Toutes les ressources**, puis filtrez sur le nom du bot d’application web. Cliquez sur la ressource du type **Application Insights**. L’icône d’Application Insights est une ampoule.

    ![Rechercher des insights d’application dans le portail Azure](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Lorsque la ressource s’ouvre, cliquez sur l’icône **Rechercher** de la loupe dans le panneau le plus à droite. Un nouveau panneau s’affiche à droite. Selon la quantité des données de télémétrie trouvées, le panneau peut prendre une seconde à afficher. Recherchez `LUIS`. La liste est réduite aux seuls résultats de requête LUIS ajoutés avec ce didacticiel.

    ![Rechercher des traces](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Sélectionnez la première entrée. Une nouvelle fenêtre affiche des données plus détaillées, dont les données personnalisées pour la requête de LUIS tout à fait à droite. Les données incluent l’intention principale et son score.

    ![Passer en revue l’élément de trace](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Lorsque vous avez terminé, cliquez sur la croix **X** en haut à droite pour revenir à la liste des éléments de dépendance.

> [!Tip]
> Si vous souhaitez enregistrer la liste des dépendances et y revenir plus tard, cliquez sur **...Plus**, puis cliquez sur **Enregistrer favori**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Interroger Application Insights pour les intentions, les scores et les énoncés

Application Insights vous permet d’interroger les données avec le langage [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), ainsi que de les exporter vers [PowerBI](https://powerbi.microsoft.com).

1. Cliquez sur **Analytics** en haut de la liste des dépendances, au-dessus de la zone de filtre.

    ![Bouton Analytics](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Une nouvelle fenêtre s’ouvre avec une fenêtre de requête dans la partie supérieure, et une fenêtre de table de données en dessous. Si vous avez déjà utilisé des bases de données, cette disposition est courante. La requête inclut tous les éléments des dernières 24 heures commençant par le nom `LUIS`. La colonne **CustomDimensions** contient les résultats de requête LUIS en tant que paires nom/valeur.

    ![Rapport d’analyse par défaut](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Pour extraire les principaux score, intention et énoncé, ajoutez ce qui suit juste au-dessus de la dernière ligne dans la fenêtre de requête :

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Exécutez la requête. Faites défiler jusqu’à l’extrême droite dans la table de données. Les nouvelles colonnes des principaux score, intention et énoncé sont disponibles. Cliquez sur la colonne d’intention principale à trier.

    ![Rapport analytique personnalisé](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)

En savoir plus sur le [langage de requête Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) ou l’[export des données vers PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi).

## <a name="learn-more-about-bot-framework"></a>En savoir plus sur Bot Framework

En savoir plus sur [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Étapes suivantes

D’autres informations que vous pourriez vouloir ajouter aux données d’Application Insights sont l’ID d’application, l’ID de version, la dernière date de modification du modèle, la dernière date d’apprentissage et la dernière date de publication. Ces valeurs peuvent être récupérées soit à partir de l’URL de point de terminaison (ID d’application et ID de version), soit à partir d’un appel de l’[API Création](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d), puis définies dans les paramètres de bot d’application web et extraites à partir de là.  

Si vous utilisez le même abonnement de point de terminaison pour plusieurs applications LUIS, vous devez également inclure l’ID d’abonnement et une propriété indiquant qu’il s’agit d’une clé partagée.

> [!div class="nextstepaction"]
> [En savoir plus sur les exemples d’énoncés](luis-how-to-add-example-utterances.md)
