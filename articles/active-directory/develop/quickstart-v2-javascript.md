---
title: Démarrage rapide Azure AD v2 JavaScript | Microsoft Docs
description: Découvrez comment les applications JavaScript peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe8c2287da7a7eabc26ff134d8bb44c5e45085f1
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203045"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Démarrage rapide : Connecter des utilisateurs et acquérir un jeton d’accès à partir d’une application JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Dans ce démarrage rapide, vous allez apprendre à utiliser un exemple de code qui montre comment une application monopage JavaScript peut connecter des comptes personnels, professionnels et scolaires et obtenir un jeton d’accès pour appeler l’API Microsoft Graph ou toute API web.

![Fonctionnement de l’exemple d’application généré par ce guide de démarrage rapide](media/quickstart-v2-javascript/javascriptspa-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Inscrire et télécharger votre application de démarrage rapide
> Vous disposez de deux options pour démarrer votre application de démarrage rapide :
> * [Express] [Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuel] [Option 2 : Inscrire et configurer manuellement vos application et exemple de code](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1 : Inscrire et configurer automatiquement votre application, puis télécharger votre exemple de code
>
> 1. Accédez au [portail Azure - Inscription d’applications (préversion)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs).
> 1. Saisissez un nom pour votre application et cliquez sur **Inscrire**.
> 1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application pour vous en un seul clic.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2 : Inscrire et configurer manuellement vos application et exemple de code
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com/) pour inscrire une application.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, puis sélectionnez **Inscriptions d’applications (préversion) > Nouvelle inscription**.
> 1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
> 1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
> 1. Sélectionnez la plateforme **Web** dans la section **URI de redirection** et définissez la valeur sur `http://localhost:30662/`.
> 1. Lorsque vous avez terminé, sélectionnez **Inscrire**.  Sur la page **Vue d’ensemble**, notez la valeur **ID d’application (client)**.
> 1. Ce démarrage rapide requiert l’activation du [flux d’octroi implicite](v2-oauth2-implicit-grant-flow.md). Dans le volet de navigation de gauche de l’application inscrite, sélectionnez **Authentification**.
> 1. Dans **Paramètres avancés**, sous **Octroi implicite**, cochez les cases **Jetons d’ID** et **Jetons d’accès**. Les jetons d’ID et jetons d’accès sont nécessaires dans la mesure où cette application doit connecter des utilisateurs et appeler une API.
> 1. Sélectionnez **Enregistrer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code de ce démarrage rapide fonctionne, vous devez ajouter un URI de redirection tel que `http://localhost:30662/` et activer **Octroi implicite**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-javascript/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-project"></a>Étape 2 : Téléchargez le projet

Vous pouvez choisir l’une de ces options en fonction de leur adéquation avec votre environnement de développement.
* [Télécharger les principaux fichiers de projet pour un serveur web, tel que Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Extrayez le fichier zip dans un dossier local, par exemple **C:\Azure-Samples**.
Pour ouvrir les fichiers dans le dossier, utilisez un éditeur tel que [Visual Studio Code](https://code.visualstudio.com/).

#### <a name="step-3-configure-your-javascript-app"></a>Étape 3 : Configurer une application JavaScript

> [!div renderon="docs"]
> Sous le dossier *JavaScriptSPA*, modifiez `index.html` et définissez les valeurs de `clientID` et de `authority` sous `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Sous le dossier *JavaScriptSPA*, modifiez `index.html` et remplacez `applicationConfig` par :

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Où :
> - `Enter_the_Application_Id_here` : est l’**ID d’application (client)** pour l’application que vous avez inscrite.
> - `Enter_the_Tenant_Info_Here` : est défini sur l’une des options suivantes :
>   - Si votre application prend en charge **Comptes dans cet annuaire organisationnel**, remplacez cette valeur avec l’**ID de locataire** ou le **nom du locataire** (par exemple, contoso.microsoft.com)
>   - Si votre application prend en charge **Comptes dans un annuaire organisationnel**, remplacez cette valeur par `organizations`
>   - Si votre application prend en charge **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**, remplacez cette valeur par `common`
>
> > [!TIP]
> > Pour connaître les valeurs de l’**ID d’Application (client)**, de l’**ID de l’annuaire (locataire)**, et des **Types de comptes pris en charge**, consultez la page **Vue d’ensemble** de l’application dans le Portail Azure.

#### <a name="step-4-run-the-project"></a>Étape 4 : Exécuter le projet

* Si vous utilisez [Node.js](https://nodejs.org/en/download/) :

    1. Exécutez la commande suivante dans le répertoire du projet pour démarrer le serveur :

        ```batch
        npm install
        node server.js
        ```

    1. Ouvrez un navigateur web et accédez à `http://localhost:30662/`.
    1. Cliquez sur le bouton **Se connecter** pour démarrer la connexion, puis appelez l’API Microsoft Graph.


* Si vous utilisez [Visual Studio](https://visualstudio.microsoft.com/downloads/), prenez soin de sélectionner la solution de projet avant d’appuyer sur **F5** pour exécuter votre projet.

## <a name="more-information"></a>Informations complémentaires

### <a name="msaljs"></a>*msal.js*

MSAL est la bibliothèque utilisée pour connecter des utilisateurs et demander des jetons permettant d’accéder à une API protégée par Microsoft Azure Active Directory (Azure AD). Le fichier *index.html* du démarrage rapide contient une référence à la bibliothèque :

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Sinon, si Node est installé, vous pouvez le télécharger via npm :

```batch
npm install msal
```

### <a name="msal-initialization"></a>Initialisation MSAL

Le code de démarrage rapide montre également comment initialiser la bibliothèque :

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |ID d’application de l’application inscrite dans le portail Azure|
> |`authority`    |Il s’agit de l’URL de l’autorité. La transmission *null* définit l’autorité par défaut sur `https://login.microsoftonline.com/common`. Si votre application est à locataire unique (c’est-à-dire qu’elle cible des comptes dans un répertoire seulement), définissez cette valeur sur `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| La méthode de rappel appelée après l’authentification redirige vers l’application. Ici, `acquireTokenRedirectCallBack` est transmis. La valeur est Null si vous utilisez loginPopup.|
> |`options`  |Un ensemble de paramètres facultatifs. Dans ce cas, la configuration de `storeAuthStateInCookie` et `cacheLocation` est facultative. Consultez le [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) pour plus d’informations sur les options. |

### <a name="sign-in-users"></a>Connexion des utilisateurs

L’extrait de code qui suit montre comment connecter des utilisateurs :

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (Facultatif) Contient les étendues demandées pour le consentement de l’utilisateur au moment de l’ouverture de session. Par exemple, `[ "user.read" ]` pour Microsoft Graph ou `[ "<Application ID URL>/scope" ]` pour les API web personnalisées (c’est-à-dire `api://<Application ID>/access_as_user`). Ici, `applicationConfig.graphScopes` est transmis. |

> [!TIP]
> Vous pouvez également utiliser la méthode `loginRedirect` pour rediriger la page actuelle vers la page de connexion et non vers une fenêtre contextuelle.

### <a name="request-tokens"></a>Requête de jetons

MSAL utilise trois méthodes pour acquérir des jetons : `acquireTokenRedirect`, `acquireTokenPopup` et `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Obtenir un jeton d’utilisateur en mode silencieux

La méthode `acquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand la méthode `loginRedirect` ou `loginPopup` est exécutée pour la première fois, c’est en général la méthode `acquireTokenSilent` qui est utilisée pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants. Les appels pour les demandes ou les renouvellements de jetons sont effectués en mode silencieux.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | Contient les étendues que vous avez demandé à retourner dans le jeton d’accès pour l’API. Par exemple, `[ "user.read" ]` pour Microsoft Graph ou `[ "<Application ID URL>/scope" ]` pour les API web personnalisées (c’est-à-dire `api://<Application ID>/access_as_user`). Ici, `applicationConfig.graphScopes` est transmis.|

#### <a name="get-a-user-token-interactively"></a>Obtenir un jeton d’utilisateur de manière interactive

Dans certaines situations, vous devez forcer les utilisateurs à interagir avec le point de terminaison Azure AD v2.0. Par exemple : 
* Les utilisateurs doivent réentrer leurs informations d’identification, car leur mot de passe a expiré
* Votre application demande l’accès à des étendues de ressource supplémentaires pour laquelle l’utilisateur doit donner son consentement
* Une authentification à 2 facteurs est demandée.

Le modèle habituellement recommandé pour la plupart des applications est le suivant : `acquireTokenSilent` d’abord, puis intercepter l’exception et appeler `acquireTokenRedirect` (ou `acquireTokenPopup`) pour démarrer une requête interactive.

Le fait d’appeler `acquireTokenPopup(scope)` entraîne l’affichage d’une fenêtre contextuelle pour la connexion (ou l’appel de `acquireTokenRedirect(scope)` entraîne la redirection des utilisateurs vers le point de terminaison Azure AD v2.0). Les utilisateurs doivent alors intervenir en confirmant leurs informations d’identification, en consentant à ce que l’application accède à la ressource ou en effectuant l’authentification à 2 facteurs.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> En raison d’un [problème connu](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) lié à la gestion des fenêtres contextuelles par le navigateur Internet Explorer, ce démarrage rapide utilise les méthodes `loginRedirect` et `acquireTokenRedirect` lorsqu’il s’agit du navigateur utilisé.

## <a name="next-steps"></a>Étapes suivantes

Pour un guide pas à pas plus détaillé sur la création de l’application pour ce démarrage rapide, consultez le didacticiel de JavaScript ci-dessous.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Découvrez les étapes permettant de créer l’application pour ce démarrage rapide

> [!div class="nextstepaction"]
> [Tutoriel pour se connecter et appeler MS Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Parcourir le référentiel MSAL pour la documentation, les FAQ, les problèmes et ainsi de suite

> [!div class="nextstepaction"]
> [Référentiel GitHub MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
