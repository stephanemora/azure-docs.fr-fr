---
title: Démarrage rapide Azure AD v2 JavaScript | Microsoft Docs
description: Découvrez comment les applications JavaScript peuvent appeler une API qui nécessite des jetons d’accès à partir d’un point de terminaison Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 69c77896f894201d1419aaef33470a02ac45ff91
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986286"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Démarrage rapide : Connecter des utilisateurs et acquérir un jeton d’accès à partir d’une application JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Dans ce démarrage rapide, vous allez apprendre à utiliser un exemple de code qui montre comment une application monopage JavaScript peut connecter des comptes personnels, professionnels et scolaires et obtenir un jeton d’accès pour appeler l’API Microsoft Graph ou toute API web.

![Fonctionnement de l’exemple d’application généré par ce démarrage rapide](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Inscrivez votre application et téléchargez votre application de démarrage rapide
>
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrire votre application
>
> 1. Connectez-vous au [portail Azure](https://portal.azure.com/) pour enregistrer une application.
> 1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
> 1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, sélectionnez ensuite **inscriptions d’applications (préversion) > Nouvelle inscription**.
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

#### <a name="step-2-download-the-project"></a>Étape 2 : Télécharger le projet

Vous pouvez choisir l’une de ces options en fonction de leur adéquation avec votre environnement de développement.
* [Télécharger les principaux fichiers de projet pour un serveur web, tel que Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [Télécharger le projet Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Extrayez le fichier zip dans un dossier local, par exemple **C:\Azure-Samples**.

#### <a name="step-3-configure-your-javascript-app"></a>Étape 3 : configurez une application JavaScript

> [!div renderon="docs"]
> Modifiez `index.html` et remplacez `Enter_the_Application_Id_here` sous `applicationConfig` par l’ID de l’application que vous venez d’enregistrer.

> [!div class="sxs-lookup" renderon="portal"]
> Modifiez `index.html` et remplacez `applicationConfig` par :

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!NOTE]
>Si vous utilisez [Node.js](https://nodejs.org/en/download/), le fichier *server.js* est configuré de sorte que le serveur commence l’écoute sur le port 30662.
> Si vous utilisez [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), le fichier *.csproj* de l’exemple de code est configuré de sorte que le serveur commence l’écoute sur le port 30662.
>

#### <a name="step-4-run-the-project"></a>Étape 4 : exécutez le projet

* Si vous utilisez Node.js :

    1. Exécutez la commande suivante dans le répertoire du projet pour démarrer le serveur :

        ```batch
        npm install
        node server.js
        ```

    1. Ouvrez un navigateur web et accédez à `http://localhost:30662/`.
    1. Cliquez sur le bouton **Se connecter** pour démarrer la connexion, puis appelez l’API Microsoft Graph.


* Si vous utilisez Visual Studio, veillez à sélectionner la solution de projet, puis appuyez sur **F5** pour exécuter votre projet.

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
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
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
> [Didacticiel pour appeler l’API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Parcourir le référentiel MSAL pour la documentation, les FAQ, les problèmes et ainsi de suite

> [!div class="nextstepaction"]
> [Référentiel GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
