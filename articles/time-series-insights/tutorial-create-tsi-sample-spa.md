---
title: 'Didacticiel : Créer une application web monopage Azure Time Series Insights | Microsoft Docs'
description: Découvrez comment créer une application web monopage qui interroge et restitue les données à partir d’un environnement Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 031e8074585426584d7ef63a103c9c2b4d90e6c3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194222"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Didacticiel : Créer une application web monopage Azure Time Series Insights

Ce didacticiel vous guide tout au long du processus de création de votre propre application web monopage (SPA) pour accéder aux données Azure Time Series Insights.

Ce tutoriel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * La conception de l’application
> * Inscription de votre application auprès d’Azure Active Directory (Azure AD)
> * Comment générer, publier et tester votre application web

> [!NOTE]
> * Le code source pour ce didacticiel est disponible sur [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * L’[exemple d’application cliente](https://insights.timeseries.azure.com/clientsample) Time Series Insights est hébergé pour présenter l’application terminée utilisée dans ce didacticiel.

Souscrivez un [abonnement Azure gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.

## <a name="prerequisites"></a>Prérequis

* Une copie gratuite de Visual Studio. Téléchargez les [versions de la Communauté 2017 ou 2019](https://www.visualstudio.com/downloads/) pour commencer.

* Les composants outils principaux IIS Express, Web Deploy et Azure Cloud Services pour Visual Studio. Ajoutez les composants en modifiant votre installation de Visual Studio.

## <a name="understand-application-design"></a>Comprendre la conception de l’application

L’exemple d’application monopage Time Series Insights SPA est la base de la conception et du code utilisés dans ce didacticiel. Le code utilise la bibliothèque cliente JavaScript de Time Series Insights. La bibliothèque cliente Time Series Insights fournit une abstraction pour les deux catégories d’API principales :

- **Méthodes wrapper pour appeler les API de requête Time Series Insights** : API REST que vous pouvez utiliser pour interroger les données Time Series Insights avec des expressions basées sur JSON. Les méthodes sont organisées sous l’espace de noms TsiClient.server de la bibliothèque.

- **Méthodes pour créer et remplir plusieurs types de contrôles graphiques** : méthodes que vous pouvez utiliser pour visualiser les données Time Series Insights dans une page web. Les méthodes sont organisées sous l’espace de noms TsiClient.ux de la bibliothèque.

Ce didacticiel utilise également des données de l’environnement Time Series Insights de l’exemple d’application. Pour plus d’informations sur la structure de l’exemple d’application Time Series Insights et la manière dont il utilise la bibliothèque cliente Time Series Insights, voir le didacticiel [Explorer la bibliothèque cliente JavaScript d’Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-with-azure-ad"></a>S’inscrire auprès d’Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Générer et publier

1. Créez un répertoire pour stocker vos fichiers de projet d’application. Ensuite, accédez à chacune des URL suivantes. Cliquez avec le bouton droit sur le lien **Brute** dans l’angle supérieur droit de la page, puis sélectionnez **Enregistrer sous** pour enregistrer les fichiers dans le répertoire de votre projet.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) : HTML et JavaScript pour la page
   - [*sampleStyles.css*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css) : feuille de style CSS

   > [!NOTE]
   > Selon le navigateur, il se peut que vous deviez modifier l’extension de fichier en .html ou .css avant d’enregistrer le fichier.

1. Vérifiez que les composants requis sont installés dans Visual Studio. Les composants outils principaux IIS Express, Web Deploy et Azure Cloud Services pour Visual Studio doivent être installés.

    [![Visual Studio – Modifier les composants installés](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Il se peut que votre interface de Visual Studio diffère légèrement des exemples présentés, en fonction de votre version et de vos paramètres de configuration.

1. Ouvrez Visual Studio et connectez-vous. Pour créer un projet d’application web, dans le menu **fichier**, sélectionnez **Ouvrir** > **Site web**.

    [![Visual Studio – Créer une solution](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Dans le volet **Ouvrir le site web**, sélectionnez le répertoire de travail dans lequel vous avez stocké les fichiers HTML et CSS, puis sélectionnez **Ouvrir**.

   [![Visual Studio – Menu fichier avec les options Ouvrir et Site web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Dans le menu **Affichage** de Visual Studio, sélectionnez **Explorateur de solutions**. Votre nouvelle solution s’ouvre. Elle contient un projet de site web (icône de globe) avec les fichiers HTML et CSS.

   [![Visual Studio – Nouvelle solution dans l’Explorateur de solutions](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Avant de publier votre application, vous devez modifier les paramètres de configuration dans le fichier *index.html*.

   1. Supprimez les commentaires des trois lignes sous le commentaire `"PROD RESOURCE LINKS"` pour basculer les dépendances de DÉVELOPPEMENT vers PRODUCTION. Supprimez les commentaires des trois lignes sous le commentaire `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Vos dépendances doivent être commentées comme dans l’exemple suivant :

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Pour configurer l’application afin d’utiliser votre ID d’inscription d’application Azure AD, modifiez la valeur `clientID` de façon à utiliser l’**ID d’application** que vous avez copié à **l’étape 3** quand vous avez [inscrit l’application pour utiliser Azure AD](#register-with-azure-ad). Si vous avez créé une **URL de déconnexion** dans Azure AD, définissez cette valeur en tant que valeur `postLogoutRedirectUri`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Par exemple :

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Lorsque vous avez fini d’apporter des modifications, enregistrez le fichier *index.html*.

1. Publiez l’application web dans votre abonnement Azure en tant qu’Azure App Service.  

   > [!NOTE]
   > Plusieurs options dans les captures d’écran présentées aux étapes suivantes sont automatiquement renseignées avec des données de votre abonnement Azure. Le chargement complet de chaque volet peut prendre quelques secondes.  

   1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nœud du projet de site web, puis sélectionnez **Publier une application web**.  

      [![Visual Studio – Sélectionnez l’option Publier l’application web de l’Explorateur de solutions](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Sélectionnez **Démarrer** pour commencer à publier votre application.

      [![Visual Studio – Volet Profil de publication](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Effectuez les sélections appropriées pour publier une nouvelle instance Azure App Service ou en utiliser une existante.

      [![Sélectionner une instance Azure App Service](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png#lightbox)

   1. Sélectionnez l’abonnement à utiliser pour publier l’application. Sélectionnez le projet **TsiSpaApp**. Ensuite, sélectionnez **OK**.

      [![Visual Studio – Volet Profil de publication d’App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Sélectionnez **Publier** pour déployer l’application web.

      [![Visual Studio – Option Publier et sortie du journal de publication](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Un journal de publication réussie s’affiche dans le volet **Sortie** de Visual Studio. Une fois le déploiement terminé, Visual Studio ouvre l’application web dans un onglet de navigateur en vous invitant à vous connecter. Une fois la connexion établie, les contrôles de Time Series Insights sont renseignés.

   1. Accédez à votre application web, puis connectez-vous pour afficher les données visuelles Time Series Insights rendues.

      [![Examiner l’application web hébergée](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png)](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png#lightbox)

## <a name="troubleshoot"></a>Résolution des problèmes  

Code d’erreur/condition | Description
---------------------| -----------
*AADSTS50011 : Aucune adresse de réponse n’est inscrite pour l’application.* | La propriété **URI de redirection** est manquante dans l’inscription Azure AD. Accédez à **Authentification** > **URI de redirection** pour l’inscription de votre application Azure AD. Vérifiez la présence de l’**URI de redirection** que vous avez eu la possibilité de spécifier lors de l’**étape 2** ou de l’**étape 4**, au moment de l’[inscription de l’application pour utiliser Azure AD](#register-with-azure-ad).
*AADSTS50011 : L’URL de réponse spécifiée dans la requête ne correspond pas aux URL de réponse configurées pour l’application : '\<GUID de l’ID d’application>'.* | La valeur `postLogoutRedirectUri` spécifiée à l’**étape 6.b** de la section [Générer et publier l’application web](#build-and-publish) doit correspondre à celle spécifiée sous **Authentification** > **URI de redirection** dans l’inscription de votre application Azure AD. |
L’application web est chargée, mais présente une page de connexion textuelle sans style avec un arrière-plan blanc. | Vérifiez que les chemins évoqués à **l’étape 6** de la section [Générer et publier l’application web](#build-and-publish) sont corrects. Si l’application web ne trouve pas les fichiers .css, la page ne sera pas mise en forme correctement.

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce tutoriel crée plusieurs services Azure en cours d’exécution. Si vous ne prévoyez pas de terminer cette série de didacticiels, nous vous conseillons de supprimer toutes les ressources afin d’éviter d’exposer des frais inutiles.

Dans le menu gauche du portail Azure :

1. Sélectionnez **Groupes de ressources**, puis choisissez le groupe de ressources que vous avez créé pour l’environnement Time Series Insights. En haut de la page, sélectionnez **Supprimer le groupe de ressources**, entrez le nom du groupe de ressources, puis choisissez **Supprimer**.
1. Sélectionnez **Groupes de ressources**, puis choisissez le groupe de ressources créé par l’accélérateur de la solution de simulation d’appareil. En haut de la page, sélectionnez **Supprimer le groupe de ressources**, entrez le nom du groupe de ressources, puis choisissez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous à présenté les point suivants :

> [!div class="checklist"]
> * La conception de l’application
> * Comment inscrire votre application auprès d’Azure AD
> * Comment générer, publier et tester votre application web

Ce didacticiel s’intègre avec Azure AD et utilise l’identité de l’utilisateur connecté pour obtenir un jeton d’accès. Pour savoir comment accéder à l’API Time Series Insights en utilisant l’identité d’une application de service ou démon, voir l’article suivant :

> [!div class="nextstepaction"]
> [Authentification et autorisation pour l’API Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
