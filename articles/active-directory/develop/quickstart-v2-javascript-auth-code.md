---
title: 'Démarrage rapide : Connexion d’utilisateurs dans des applications monopages JavaScript (SPA) avec du code d’authentification | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, découvrez comment une application monopage (SPA) JavaScript peut connecter les utilisateurs de comptes personnels, professionnels et scolaires à l’aide du flux de code d’autorisation.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/09/2021
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 5a136437f6ec47cf1b60f6a8a54ac7e63139afd7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611975"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow-with-pkce"></a>Démarrage rapide : Connexion d’utilisateurs et récupération d’un jeton d’accès dans une application monopage JavaScript à l’aide du flux de code d’authentification avec PKCE

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application monopage JavaScript peut connecter des utilisateurs et appeler Microsoft Graph en utilisant le flux du code d’autorisation avec PKCE (Proof Key for Code Exchange). L’exemple de code montre comment obtenir un jeton d’accès pour appeler l’API Microsoft Graph ou n’importe quelle API web.

Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure – [Créer un abonnement Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Inscrire et télécharger votre application de démarrage rapide
> Pour démarrer votre application du guide de démarrage rapide, utilisez une des options suivantes.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 (Express) : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">portail Azure - Inscriptions d’applications</a>.
> 1. Entrez un nom pour votre application.
> 1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
> 1. Sélectionnez **Inscription**.
> 1. Accédez au volet du démarrage rapide et suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 (manuelle) : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
>
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoires + abonnements** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour basculer vers le locataire dans lequel vous voulez inscrire l’application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Entrez un **nom** pour votre application. Les utilisateurs de votre application peuvent voir ce nom, et vous pouvez le changer ultérieurement.
> 1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
> 1. Sélectionnez **Inscription**. Dans la page **Vue d’ensemble**, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.
> 1. Sous **Gérer**, sélectionnez **Authentification**.
> 1. Sous **Configurations de plateformes**, sélectionnez **Ajouter une plateforme**. Dans le volet qui s’ouvre, sélectionnez **Application monopage**.
> 1. Choisissez `http://localhost:3000/` comme valeur pour **URI de redirection**.
> 1. Sélectionnez **Configurer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, ajoutez l’**URI de redirection** `http://localhost:3000/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-javascript/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

> [!div renderon="docs"]
> Pour exécuter le projet avec un serveur web en utilisant Node.js, [téléchargez les fichiers principaux du projet](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Exécuter le projet avec un serveur web en utilisant Node.js

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Étape 3 : Configurer une application JavaScript
>
> Dans le dossier *app*, ouvrez le fichier *authConfig.js*, puis mettez à jour les valeurs `clientID`, `authority` et `redirectUri` dans l’objet `msalConfig`.
>
> ```javascript
> // Config object to be passed to MSAL on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Modifiez les valeurs dans la section `msalConfig` :
>
> - `Enter_the_Application_Id_Here` est **l’ID d’application (client)** de l’application que vous avez inscrite.
>
>    Pour connaître les valeurs de l’**ID d’application (client)** , consultez la page **Vue d’ensemble** de l’inscription d’application dans le portail Azure.
> - `Enter_the_Cloud_Instance_Id_Here` est l’instance du cloud Azure. Pour le cloud Azure principal ou mondial, entrez `https://login.microsoftonline.com`. Pour les clouds **nationaux** (par exemple, Chine), consultez [Clouds nationaux](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` prend l’une des valeurs suivantes :
>   - Si votre application prend en charge les *comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’**ID de locataire** ou le **Nom du locataire**. Par exemple : `contoso.microsoft.com`.
>
>    Pour connaître les valeurs de l’**Annuaire (locataire)** , consultez la page **Vue d’ensemble** de l’inscription d’application dans le portail Azure.
>   - Si votre application prend en charge les *comptes dans un annuaire organisationnel*, remplacez cette valeur par `organizations`.
>   - Si votre application prend en charge les *comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par `common`. **Dans le cadre de ce guide de démarrage rapide**, utilisez `common`.
>   - Pour limiter la prise en charge aux *comptes Microsoft personnels uniquement*, remplacez cette valeur par `consumers`.
>
>    Pour connaître les valeurs des **Types de compte pris en charge**, consultez la page **Vue d’ensemble** de l’inscription d’application dans le portail Azure.
> - `Enter_the_Redirect_Uri_Here` a la valeur `http://localhost:3000/`.
>
> La valeur `authority` dans votre fichier *authConfig.js* doit se présenter ainsi si vous utilisez le cloud Azure principal (mondial) :
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
>
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application.

> [!div renderon="docs"]
>
> Ensuite, ouvrez le fichier *graphConfig.js* pour mettre à jour les valeurs `graphMeEndpoint` et `graphMailEndpoint` dans l’objet `apiConfig`.
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>
> [!div renderon="docs"]
>
> `Enter_the_Graph_Endpoint_Here` est le point de terminaison sur lequel les appels d’API sont effectués. Pour le service API Microsoft Graph principal (mondial), entrez `https://graph.microsoft.com/` (en incluant la barre oblique de fin). Pour plus d’informations sur Microsoft Graph sur les clouds nationaux, consultez [Déploiement cloud national](/graph/deployments).
>
> Si vous utilisez le service API Microsoft Graph principal (mondial), les valeurs `graphMeEndpoint` et `graphMailEndpoint` dans le fichier *graphConfig.js* doivent se présenter ainsi :
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>Étape 4 : Exécuter le projet

Exécutez le projet avec un serveur web en utilisant Node.js.

1. Pour démarrer le serveur, exécutez les commandes suivantes dans le répertoire du projet :

    ```console
    npm install
    npm start
    ```

1. Accédez à `http://localhost:3000/`.

1. Sélectionnez **Se connecter** pour lancer le processus de connexion, puis appelez l’API Microsoft Graph.

    La première fois que vous vous connectez, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter. Quand vous êtes connecté, les informations de votre profil utilisateur s’affichent sur la page.

## <a name="more-information"></a>Informations complémentaires

### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

![Diagramme montrant le flux de code d’autorisation pour une application monopage.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>MSAL.js

La bibliothèque MSAL.js connecte les utilisateurs et demande les jetons utilisés pour accéder à une API protégée par la plateforme d’identités Microsoft. Le fichier *index.html* de l’exemple contient une référence à la bibliothèque :

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Si vous avez déjà installé Node.js, vous pouvez télécharger la dernière version à l’aide du gestionnaire de package Node.js (npm) :

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Étapes suivantes

Pour un guide pas à pas plus détaillé sur la création de l’application utilisée dans ce guide de démarrage rapide, consultez le tutoriel suivant :

> [!div class="nextstepaction"]
> [Tutoriel pour se connecter et appeler MS Graph](./tutorial-v2-javascript-auth-code.md)
