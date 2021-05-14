---
title: 'Démarrage rapide : Connecter des utilisateurs dans des applications monopages Angular - Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous découvrez de quelle manière une application Angular peut appeler une API qui exige des jetons d’accès émis par la plateforme d’identités Microsoft.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: e30ed370ea5858d97f2cc0a64bc92d9bc9e64b3f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108128538"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Démarrage rapide : Connecter des utilisateurs et obtenir un jeton d’accès dans une application monopage Angular

Dans ce guide de démarrage rapide, vous téléchargez et exécutez un exemple de code qui montre comment une application monopage Angular peut connecter des utilisateurs et appeler Microsoft Graph. L’exemple de code montre comment obtenir un jeton d’accès pour appeler l’API Microsoft Graph ou n’importe quelle API web.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. [Créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) pour modifier les fichiers de projet, ou [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) pour exécuter le projet.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>Inscrire et télécharger l’application de démarrage rapide
>
> Pour démarrer l’application de démarrage rapide, utilisez une des options suivantes.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>Option 1 (express) : Inscrire et configurer automatiquement l’application, puis télécharger l’exemple de code
>
> 1. Accédez à l’expérience de démarrage rapide <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Portail Azure - Inscriptions d’applications</a>.
> 1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
> 1. Dans le volet de démarrage rapide, recherchez le guide de démarrage rapide Angular. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>Option 2 (manuelle) : Inscrire et configurer manuellement l’application et l’exemple de code
>
> #### <a name="step-1-register-the-application"></a>Étape 1 : Enregistrement de l’application
>
> 1. Suivez les instructions pour [inscrire une application monopage](./scenario-spa-app-registration.md) dans le portail Azure.
> 1. Ajoutez une nouvelle plateforme dans le volet **Authentification** de l’inscription de votre application, puis enregistrez l’URI de redirection : `http://localhost:4200/`.
> 1. Ce guide de démarrage rapide utilise le [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md). Dans la section **Implicit grant and hybrid flows** (Flux d’octroi implicite et flux hybride), sélectionnez **Jetons d’ID** et **Jetons d’accès**. Des jetons d’ID et des jetons d’accès sont nécessaires, car cette application connecte les utilisateurs et appelle une API.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>Étape 1 : Configurer l’application dans le portail Azure
>
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez ajouter une URI de redirection à `http://localhost:4200/` et activer l’**octroi implicite**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-javascript/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-code-sample"></a>Étape 2 : Télécharger l’exemple de code
>[!div renderon="docs"]
>Pour exécuter le projet avec un serveur web en utilisant Node.js, clonez l’[exemple de dépôt](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) ou [téléchargez les fichiers projet principaux](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Ouvrez les fichiers dans un éditeur tel que Visual Studio Code.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip).

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>Étape 3 : Configurer l’application JavaScript
>
> Dans le dossier *src/app*, modifiez *app.module.ts* et définissez les valeurs `clientId` et `authority` sous `MsalModule.forRoot`.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Remplacez les valeurs suivantes :
>
>|Nom de la valeur|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|Dans la page **Vue d’ensemble** de l’inscription de votre application, il s’agit de la valeur de votre **ID d’application (client)** . |
>|Enter_the_Cloud_Instance_Id_Here|Il s’agit de l’instance du cloud Azure. Pour le cloud Azure principal ou mondial, entrez `https://login.microsoftonline.com`. Pour les clouds nationaux (par exemple Chine), consultez [Clouds nationaux](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Définissez cette valeur sur une des options suivantes : Si votre application prend en charge les *comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’ID de l’annuaire (locataire) ou le nom du locataire (par exemple, `contoso.microsoft.com`). Si votre application prend en charge les *comptes dans un annuaire organisationnel*, remplacez cette valeur par `organizations`. Si votre application prend en charge les *comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par `common`. Pour limiter la prise en charge aux *comptes Microsoft personnels uniquement*, remplacez cette valeur par `consumers`. |
>|Enter_the_Redirect_Uri_Here|Remplacez par `http://localhost:4200` .|
>|cacheLocation  | (Facultatif) Définissez le stockage du navigateur pour l’état d’authentification. Par défaut, il s’agit de `sessionStorage`.   |
>|storeAuthStateInCookie  | (Facultatif) Identifiez la bibliothèque qui stocke l’état de la demande d’authentification. Cet état est indispensable pour valider les flux d’authentification dans les cookies du navigateur. Ce cookie est configuré pour Internet Explorer et Microsoft Edge, afin de pouvoir prendre en charge ces deux navigateurs. Pour plus de détails, consultez les [problèmes connus](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
>
> Pour connaître les valeurs de l’**ID d’Application (client)** , de l’**ID de l’annuaire (locataire)** , et des **Types de comptes pris en charge**, consultez la page **Vue d’ensemble** de l’application dans le Portail Azure.

> Pour plus d’informations sur les options configurables disponibles, consultez [Initialiser les applications clientes](msal-js-initializing-client-applications.md).

> Vous trouverez le code source de la bibliothèque MSAL.js dans le dépôt [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) sur GitHub.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application.

> [!div renderon="docs"]
>
> Faites défiler le contenu du même fichier et mettez à jour la valeur `graphMeEndpoint`. 
> - Remplacez la chaîne `Enter_the_Graph_Endpoint_Herev1.0/me` par `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` est le point de terminaison sur lequel les appels d’API seront effectués. Pour le service API Microsoft Graph principal (mondial), entrez `https://graph.microsoft.com/` (en incluant la barre oblique de fin). Pour plus d’informations, consultez la [documentation](/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>Étape 4 : Exécuter le projet

Si vous utilisez Node.js :

1. Démarrez le serveur en exécutant les commandes suivantes à partir du répertoire du projet :

   ```console
   npm install
   npm start
   ```

1. Accédez à `http://localhost:4200/`.
1. Sélectionnez **Connexion**. La première fois que vous vous connectez, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter automatiquement.
1. Sélectionnez **Profil** pour appeler Microsoft Graph. Vos informations de profil utilisateur s’affichent dans la page.

## <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

![Diagramme illustrant le fonctionnement de l’exemple d’application dans ce guide de démarrage rapide](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment connecter un utilisateur et acquérir des jetons dans le tutoriel Angular :

> [!div class="nextstepaction"]
> [Tutoriel Angular](./tutorial-v2-angular.md)