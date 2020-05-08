---
title: Connecter des utilisateurs dans des applications monopages JavaScript avec du code d’authentification | Azure
titleSuffix: Microsoft identity platform
description: Découvrez de quelle manière une application JavaScript peut appeler une API qui nécessite des jetons d’accès à l’aide de la plateforme d’identités Microsoft.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871510"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Démarrage rapide : Connecter des utilisateurs et obtenir un jeton d’accès dans une application monopage JavaScript en utilisant le flux du code d’authentification 

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être changés avant la disponibilité générale (GA).


Ce démarrage rapide utilise MSAL.js 2.0 avec le flux du code d’autorisation. Pour utiliser MSAL.js 1.0 avec le flux implicite, consultez [ce démarrage rapide](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

Dans ce guide de démarrage rapide, vous utilisez un exemple de code pour découvrir comment une application monopage JavaScript peut connecter les utilisateurs de comptes personnels, professionnels et scolaires. Une application monopage JavaScript peut également obtenir un jeton d’accès pour appeler l’API Microsoft Graph ou une API web. Consultez [Fonctionnement de l’exemple](#how-the-sample-works) pour obtenir une illustration.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure – [Créer un abonnement Azure gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (pour modifier des fichiers projet)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Inscrire et télécharger votre application de démarrage rapide
> Pour démarrer votre application du guide de démarrage rapide, utilisez une des options suivantes.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 (Express) : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com).
> 1. Si votre compte vous donne accès à plusieurs locataires, sélectionnez le compte en haut à droite, puis définissez votre session de portail sur le locataire Azure Active Directory (Azure AD) que vous voulez utiliser.
> 1. Sélectionnez [Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Entrez un nom pour votre application.
> 1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
> 1. Sélectionnez **Inscription**.
> 1. Accédez au volet du démarrage rapide et suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 (manuelle) : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com).
>
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite, puis définissez votre session de portail sur le locataire Azure AD que vous voulez utiliser.
> 1. Sélectionnez [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908).
> 1. Sélectionnez **Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
> 1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
> 1. Sélectionnez **Inscription**. Dans la page **Vue d’ensemble**, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.
> 1. Dans le volet gauche de l’application inscrite, sélectionnez **Authentification**.
> 1. Sous **Configurations de plateforme**, sélectionnez **Ajouter une plateforme**. Un panneau s’ouvre sur la gauche. Sélectionnez-y la région **Applications monopages**.
> 1. Toujours sur la gauche, définissez la valeur de l’**URI de redirection** sur `http://localhost:3000/`. 
> 1. Sélectionnez **Configurer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce démarrage rapide fonctionne, vous devez ajouter un `redirectUri` de type `http://localhost:3000/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-javascript/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

> [!div renderon="docs"]
> Pour exécuter le projet avec un serveur web en utilisant Node.js, [téléchargez les fichiers principaux du projet](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Exécuter le projet avec un serveur web en utilisant Node.js

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [Téléchargez l’exemple de code](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>Étape 3 : Configurer une application JavaScript
>
> Dans le dossier *app*, modifiez *authConfig.js*, puis définissez les valeurs `clientID`, `authority` et `redirectUri` sous `msalConfig`.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> Où :
> - *\<Enter_the_Application_Id_Here>* est l’**ID d’application (client)** de l’application que vous avez inscrite.
> - *\<Enter_the_Cloud_Instance_Id_Here>* est l’instance du cloud Azure. Pour le cloud Azure principal ou mondial, entrez simplement *https://login.microsoftonline.com/* . Pour les clouds **nationaux** (par exemple, Chine), consultez [Clouds nationaux](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - *\<Enter_the_Tenant_info_here >* est défini sur une des options suivantes :
>    - Si votre application prend en charge les *Comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’**ID de locataire** ou le **nom du locataire** (par exemple, *contoso.microsoft.com*).
>    - Si votre application prend en charge les *Comptes dans un annuaire organisationnel*, remplacez cette valeur par **organizations**.
>    - Si votre application prend en charge les *Comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par **common**. Pour limiter la prise en charge aux *Comptes Microsoft personnels uniquement*, remplacez cette valeur par **consumers**.
> - *\<Enter_the_Redirect_Uri_Here>* prend la valeur de `http://localhost:3000`
> > [!TIP]
> > Pour trouver les valeurs de l’**ID d’application (client)** , de l’**ID de l’annuaire (locataire)** et des **Types de comptes pris en charge**, accédez à la page **Vue d’ensemble** de l’inscription de l’application sur le portail Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Étape 3 : Votre application est configurée et prête à être exécutée
> Nous avons configuré votre projet avec les valeurs des propriétés de votre application.

> [!div renderon="docs"]
>
> Ensuite, dans le même dossier, modifiez le fichier *graphConfig.js* pour définir `graphMeEndpoint` et `graphMailEndpoint` pour l’objet `apiConfig`.
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
> *\<Enter_the_Graph_Endpoint_Here>* est le point de terminaison sur lequel les appels d’API seront effectués. Pour le service d’API Microsoft Graph principal ou mondial, entrez `https://graph.microsoft.com`. Pour plus d’informations, consultez [Déploiements dans les clouds nationaux](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>Étape 4 : Exécuter le projet

Exécutez le projet avec un serveur web en utilisant [Node.js](https://nodejs.org/en/download/) :

1. Pour démarrer le serveur, exécutez les commandes suivantes dans le répertoire du projet :
    ```bash
    npm install
    npm start
    ```
1. Accédez à `http://localhost:3000/`.

1. Sélectionnez **Se connecter** pour lancer le processus de connexion, puis appelez l’API Microsoft Graph.

    La première fois que vous vous connectez, vous êtes invité à autoriser l’application à accéder à votre profil et à vous connecter. Une fois que vous êtes connecté, les informations de votre profil utilisateur doivent s’afficher sur la page.

## <a name="more-information"></a>Informations complémentaires

### <a name="how-the-sample-works"></a>Fonctionnement de l’exemple

![Fonctionnement de l’exemple d’application monopage JavaScript : 1. L’application monopage initie la connexion. 2. L’application monopage acquiert un jeton d’ID auprès de la plateforme d’identité Microsoft. 3. Les appels de l’application monopage acquièrent le jeton. 4. La plateforme d’identité Microsoft retourne un jeton d’accès à l’application monopage. 5. L’application monopage adresse une requête HTTP GET à l’API Microsoft Graph avec le jeton d’accès. 6. L’API Graph retourne une réponse HTTP à l’application monopage.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

La bibliothèque MSAL.js connecte les utilisateurs et demande les jetons utilisés pour accéder à une API protégée par la plateforme d’identités Microsoft. Le fichier *index.html* de l’exemple contient une référence à la bibliothèque :

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> Vous pouvez remplacer la version précédente par la dernière version publiée sous [Publications MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

Sinon, si vous avez installé Node.js, vous pouvez télécharger la dernière version à l’aide de Node.js Package Manager (npm) :

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Étapes suivantes

Le [dépôt MSAL.js sur GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) contient des informations supplémentaires sur la bibliothèque, des questions fréquentes (FAQ) et une section sur la résolution des problèmes.

Pour obtenir un guide pas à pas plus détaillé sur la création de l’application pour ce guide de démarrage rapide, consultez :

> [!div class="nextstepaction"]
> [Tutoriel pour se connecter et appeler MS Graph](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
