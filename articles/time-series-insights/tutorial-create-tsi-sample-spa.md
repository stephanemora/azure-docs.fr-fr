---
title: 'Didacticiel : Créer une application web monopage Azure Time Series Insights | Microsoft Docs'
description: Découvrez comment créer une application web monopage qui interroge et restitue les données à partir d’un environnement Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 3c2de4bd1f9d487cbb58be9581a0395bf1caa3f9
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991899"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Didacticiel : Créer une application web monopage Azure Time Series Insights

Ce didacticiel vous guide tout au long du processus de création de votre propre application web monopage (SPA) pour accéder aux données Azure Time Series Insights. 

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * La conception de l’application
> * Inscription de votre application auprès d’Azure Active Directory (Azure AD)
> * Comment générer, publier et tester votre application web

> [!NOTE]
> * Le code source pour ce didacticiel est disponible sur [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * L’[exemple d’application cliente](https://insights.timeseries.azure.com/clientsample) Time Series Insights est hébergé pour présenter l’application terminée utilisée dans ce didacticiel.

## <a name="prerequisites"></a>Conditions préalables

* Souscrivez un [abonnement Azure gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.

* Une copie gratuite de Visual Studio. Téléchargez les [versions de la Communauté 2017 ou 2019](https://www.visualstudio.com/downloads/) pour commencer.

* Les composants outils principaux IIS Express, Web Deploy et Azure Cloud Services pour Visual Studio. Ajoutez les composants en modifiant votre installation de Visual Studio.

## <a name="application-design"></a>Conception des applications

L’exemple d’application monopage Time Series Insights SPA est la base de la conception et du code utilisés dans ce didacticiel. Le code utilise la bibliothèque cliente JavaScript de Time Series Insights. La bibliothèque cliente Time Series Insights fournit une abstraction pour les deux catégories d’API principales :

- **Méthodes wrapper pour appeler les API de requête Time Series Insights** : API REST que vous pouvez utiliser pour interroger les données Time Series Insights avec des expressions basées sur JSON. Les méthodes sont organisées sous l’espace de noms TsiClient.server de la bibliothèque.

- **Méthodes pour créer et remplir plusieurs types de contrôles graphiques** : méthodes que vous pouvez utiliser pour visualiser les données Time Series Insights dans une page web. Les méthodes sont organisées sous l’espace de noms TsiClient.ux de la bibliothèque.

Ce didacticiel utilise également des données de l’environnement Time Series Insights de l’exemple d’application. Pour plus d’informations sur la structure de l’exemple d’application Time Series Insights et la manière dont il utilise la bibliothèque cliente Time Series Insights, voir le didacticiel [Explorer la bibliothèque cliente JavaScript d’Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Inscrire l’application auprès d’Azure AD

Avant de générer l’application, vous devez l’inscrire auprès d’Azure AD. L’inscription fournissant la configuration de l’identité, l’application peut utiliser la prise en charge d’OAuth pour l’authentification unique. La norme OAuth requiert que les applications monopages utilisent le type d’autorisation implicite. Vous mettez à jour l’autorisation dans le manifeste de l’application. Un manifeste d’application est une représentation JSON de la configuration de l’identité d’une application.

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte d’abonnement Azure.  
1. Sélectionnez **Azure Active Directory** > **Inscriptions des applications** > **Nouvelle inscription d’application**.

   [![Portail Azure – Commencer l’inscription de l’application Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. Sur la page **Créer**, renseignez les paramètres requis.

   Paramètre|Description
   ---|---
   **Nom** | Entrez un nom d’inscription explicite.  
   **Type d’application** | Laissez **Application web/API**.
   **URL d’authentification** | Entrez l’URL de la page de connexion (accueil) de l’application. L’application étant destinée à être hébergée dans Azure App Service, vous devez utiliser une URL du domaine https:\//azurewebsites.net. Dans cet exemple, le nom est basé sur le nom de l’inscription.

   Sélectionnez **Créer** pour créer la nouvelle inscription de l’application.

   [![Portail Azure – Option Créer dans le volet d’inscription d’application Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Les ressources d’application fournissent des API REST que d’autres applications peuvent utiliser. Les API sont également inscrites auprès d’Azure AD. Les API fournissent un accès granulaire et sécurisé aux applications clientes en exposant des *étendues*. Étant donné que votre application appelle l’API Azure Time Series Insights, vous devez spécifier l’API et l’étendue. Une autorisation est accordée pour l’API et l’étendue lors de l’exécution du runtime. Sélectionnez **Paramètres** > **Autorisations requises** > **Ajouter**.

   [![Portail Azure – Option Ajouter pour l’ajout d’autorisations Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Dans le volet **Ajouter un accès API**, sélectionnez **1 Sélectionner une API** pour spécifier l’API Azure Time Series Insights. Sur la page **Sélectionner une API**, dans la zone de recherche, entrez **azure time**. Ensuite, dans la liste des résultats, sélectionnez **Azure Time Series Insights**. Choisissez **Select**.

   [![Portail Azure – Option de recherche pour l’ajout d’autorisations Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Pour sélectionner une étendue pour l’API, dans le volet **Ajouter un accès d’API**, choisissez **2 Sélectionner des autorisations**. Sur la page **Activer l’accès**, sélectionnez l’étendue **Access Azure Time Series Insights service** (Accéder au service Azure Time Series Insights). Choisissez **Select**. Vous êtes redirigé vers le volet **Ajouter un accès d’API**. Sélectionnez **Terminé**.

   [![Portail Azure – Définir une étendue pour l’ajout d’autorisations Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. Dans le volet **Autorisations requises**, l’API Azure Time Series Insights est à présent affichée. Vous devez également donner un consentement préalable à l’autorisation d’accès de l’application à l’API et à l’étendue, pour tous les utilisateurs. Sélectionnez **Accorder des autorisations**, puis **Oui**.

   [![Portail Azure – Option Accorder des autorisations pour l’ajout des autorisations Azure AD requises](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Comme indiqué précédemment, vous devez également actualiser le manifeste de l’application. Dans le menu horizontal en haut du volet (la barre de navigation), sélectionnez le nom de l’application pour revenir au volet **Application inscrite**. Sélectionnez **Manifeste**, modifiez la propriété `oauth2AllowImplicitFlow` en la définissant sur `true`, puis sélectionnez **Enregistrer**.

   [![Portail Azure – Mettre à jour le manifeste Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. Dans la barre de navigation, sélectionnez le nom de l’application pour revenir au volet **Application inscrite**. Copiez les valeurs de **Page d’accueil** et d’**ID d’Application** pour votre application. Vous utiliserez ces propriétés plus tard dans le didacticiel.

   [![Portail Azure – Copier les valeurs d’URL de la page d’accueil et d’ID de l’Application pour votre application](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Générer et publier l’application web

1. Créez un répertoire pour stocker vos fichiers de projet d’application. Ensuite, accédez à chacune des URL suivantes. Cliquez avec le bouton droit sur le lien **Brute** dans l’angle supérieur droit de la page, puis sélectionnez **Enregistrer sous** pour enregistrer les fichiers dans le répertoire de votre projet.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html) : HTML et JavaScript pour la page
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css) : Feuille de style CSS

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

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

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

   1. Pour configurer l’application afin d’utiliser votre ID d’inscription d’application Azure AD, modifiez les valeurs `clientID` et `postLogoutRedirectUri` de façon à utiliser les valeurs d’**ID d’Application** et de **Page d’accueil** que vous avez copiées à l’étape 9 de la section [ Inscrire l’application auprès d’Azure AD](#register-the-application-with-azure-ad).

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

   1. Sélectionnez l’abonnement à utiliser pour publier l’application. Sélectionnez le projet **TsiSpaApp**. Ensuite, sélectionnez **OK**.

      [![Visual Studio – Volet Profil de publication d’App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Sélectionnez **Publier** pour déployer l’application web.

      [![Visual Studio – Option Publier et sortie du journal de publication](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Un journal de publication réussie s’affiche dans le volet **Sortie** de Visual Studio. Une fois le déploiement terminé, Visual Studio ouvre l’application web dans un onglet de navigateur en vous invitant à vous connecter. Une fois la connexion établie, les contrôles de Time Series Insights sont renseignés.

## <a name="troubleshoot"></a>Résolution des problèmes  

Code d’erreur/condition | Description
---------------------| -----------
*AADSTS50011 : Aucune adresse de réponse n’est inscrite pour l’application.* | La propriété **URL de réponse** est manquante dans l’inscription Azure AD. Accédez à **Paramètres** > **URL de réponse** pour votre inscription d’application Azure AD. Vérifiez que l’**URL de connexion** spécifiée à l’étape 3 de la section [Inscrire l’application auprès d’Azure AD](#register-the-application-with-azure-ad) est présente.
*AADSTS50011 : L’URL de réponse spécifiée dans la requête ne correspond pas aux URL de réponse configurées pour l’application : '\<GUID de l’ID d’application>'.* | La valeur `postLogoutRedirectUri` spécifiée à l’étape 6 de la section [Générer et publier l’application web](#build-and-publish-the-web-application) doit correspondre à celle spécifiée sous **Paramètres** > **URL de réponse** dans votre inscription d’application Azure AD. Veillez à modifier également la valeur **URL de Destination** afin d’utiliser *https*, comme indiqué à l’étape 5 de la section [Générer et publier l’application web](#build-and-publish-the-web-application).
L’application web est chargée, mais présente une page de connexion textuelle sans style avec un arrière-plan blanc. | Vérifiez que les chemins d’accès évoqués à l’étape 4 de la section [Générer et publier l’application web](#build-and-publish-the-web-application) sont corrects. Si l’application web ne trouve pas les fichiers .css, la page ne sera pas mise en forme correctement.

## <a name="clean-up-resources"></a>Supprimer des ressources

Ce didacticiel crée plusieurs services Azure en cours d’exécution. Si vous ne prévoyez pas de terminer cette série de didacticiels, nous vous conseillons de supprimer toutes les ressources afin d’éviter d’exposer des frais inutiles.

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
