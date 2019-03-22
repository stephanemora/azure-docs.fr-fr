---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 0f4f890afd8c698bec8dbd76e8bb8dca710f4d6c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203347"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utiliser Microsoft Authentication Library (MSAL) pour connecter l’utilisateur

1. Ajoutez le code ci-après à votre fichier `index.html` entre les balises `<script></script>` :

```javascript
//Pass null for default authority (https://login.microsoftonline.com/common)
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack,
    {storeAuthStateInCookie: true, cacheLocation: "localStorage"});

function signIn() {
    myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
        //Login Success
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }, function (error) {
        console.log(error);
    });
}

function acquireTokenPopupAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
        callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        // Call acquireTokenPopup (popup window) in case of acquireTokenSilent failure due to consent or interaction required ONLY
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
                callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
            }, function (error) {
                console.log(error);
            });
        }
    });
}

function graphAPICallback(data) {
    //Display user data on DOM
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += " to Microsoft Graph API!!";
    document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
}

function showWelcomeMessage() {
    var divWelcome = document.getElementById('WelcomeMessage');
    divWelcome.innerHTML += 'Welcome ' + myMSALObj.getUser().name;
    var loginbutton = document.getElementById('SignIn');
    loginbutton.innerHTML = 'Sign Out';
    loginbutton.setAttribute('onclick', 'signOut();');
}

// This function can be removed if you do not need to support IE
function acquireTokenRedirectAndCallMSGraph() {
    //Call acquireTokenSilent (iframe) to obtain a token for Microsoft Graph
    myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
      callMSGraph(applicationConfig.graphEndpoint, accessToken, graphAPICallback);
    }, function (error) {
        console.log(error);
        //Call acquireTokenRedirect in case of acquireToken Failure
        if (error.indexOf("consent_required") !== -1 || error.indexOf("interaction_required") !== -1 || error.indexOf("login_required") !== -1) {
            myMSALObj.acquireTokenRedirect(applicationConfig.graphScopes);
        }
    });
}

function acquireTokenRedirectCallBack(errorDesc, token, error, tokenType) {
    if (tokenType === "access_token") {
        callMSGraph(applicationConfig.graphEndpoint, token, graphAPICallback);
    } else {
        console.log("token type is:"+tokenType);
    }
}


// Browser check variables
var ua = window.navigator.userAgent;
var msie = ua.indexOf('MSIE ');
var msie11 = ua.indexOf('Trident/');
var msedge = ua.indexOf('Edge/');
var isIE = msie > 0 || msie11 > 0;
var isEdge = msedge > 0;

//If you support IE, our recommendation is that you sign-in using Redirect APIs
//If you as a developer are testing using Microsoft Edge InPrivate mode, please add "isEdge" to the if check
if (!isIE) {
    if (myMSALObj.getUser()) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenPopupAndCallMSGraph();
    }
} else {
    document.getElementById("SignIn").onclick = function () {
        myMSALObj.loginRedirect(applicationConfig.graphScopes);
    };
    if (myMSALObj.getUser() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
        showWelcomeMessage();
        acquireTokenRedirectAndCallMSGraph();
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires

Quand un utilisateur clique sur le bouton **Se connecter** pour la première fois, la méthode `signIn` appelle `loginPopup` pour le connecter. Cette méthode ouvre une fenêtre contextuelle avec le *point de terminaison Microsoft Azure Active Directory v2.0* pour demander et valider les informations d’identification de l’utilisateur. Une fois connecté, l’utilisateur est redirigé vers la page *index.html* d’origine. Ensuite, un jeton est reçu, puis traité par `msal.js`, et les informations contenues dans le jeton sont mises en cache. Ce jeton est le *jeton d’ID* et contient des informations de base sur l’utilisateur, telles que son nom d’affichage. Si vous envisagez d’utiliser les données fournies par ce jeton à toutes fins que ce soit, vous devez vous assurer que ce jeton est validé par le serveur principal afin de garantir qu’il a été émis pour un utilisateur valide de votre application.

L’application SPA créée dans ce guide appelle `acquireTokenSilent` et/ou `acquireTokenPopup` pour acquérir un *jeton d’accès* utilisé pour demander les informations de profil utilisateur à l’API Microsoft Graph. Si vous avez besoin d’un exemple qui valide le jeton d’ID, regardez [cet exemple d’application](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "exemple GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2") dans GitHub. Cet exemple utilise une API web ASP.NET pour la validation du jeton.

#### <a name="getting-a-user-token-interactively"></a>Obtention d’un jeton d’utilisateur de manière interactive

Après la première connexion, il n’est pas souhaitable de demander aux utilisateurs de se réauthentifier chaque fois qu’ils ont besoin d’un jeton pour accéder à une ressource. Par conséquent, *acquireTokenSilent* doit être utilisé pour acquérir des jetons dans la plupart des situations. Il existe cependant des situations dans lesquelles vous devez forcer les utilisateurs à interagir avec le point de terminaison Azure Active Directory v2.0. Voici quelques exemples de telles situations :

- Les utilisateurs doivent réentrer leurs informations d’identification, car le mot de passe a expiré.
- Votre application demande l’accès à une ressource pour laquelle l’utilisateur doit donner son consentement.
- Une authentification à 2 facteurs est demandée.

Le fait d’appeler *acquireTokenRedirect(scope)* ouvre une fenêtre contextuelle (ou *acquireTokenRedirect(scope)* et redirige les utilisateurs vers le point de terminaison Azure Active Directory v2.0) où ils doivent soit confirmer leurs informations d’identification en autorisant l’application à accéder à la ressource requise, soit effectuer l’authentification à 2 facteurs.

#### <a name="getting-a-user-token-silently"></a>Obtention d’un jeton d’utilisateur en mode silencieux

La méthode ` acquireTokenSilent` gère les acquisitions et renouvellements de jetons sans aucune interaction de l’utilisateur. Quand `loginPopup` (ou `loginRedirect`) est exécuté pour la première fois, c’est en général la méthode `acquireTokenSilent` qui est utilisée pour obtenir les jetons permettant d’accéder aux ressources protégées pour les appels suivants, car les appels de demande ou de renouvellement des jetons sont émis de manière silencieuse.
`acquireTokenSilent` risque d’échouer dans certains cas (par exemple, si le mot de passe a expiré). Votre application peut gérer cette exception de deux manières :

1. En appelant immédiatement `acquireTokenPopup` pour que l’utilisateur soit invité à se connecter. Cette méthode est couramment employée avec les applications en ligne dans lesquelles aucun contenu non authentifié n’est disponible pour l’utilisateur. L’exemple créé avec cette installation guidée utilise ce modèle.

2. Les applications peuvent également signaler à l’utilisateur qu’une connexion interactive est requise afin qu’il puisse choisir le bon moment pour se connecter ou que l’application puisse réessayer d’exécuter `acquireTokenSilent` ultérieurement. Cette solution est couramment retenue lorsque l’utilisateur peut utiliser d’autres fonctionnalités de l’application sans être perturbé, notamment lorsque du contenu non authentifié est disponible dans l’application. Dans ce cas, l’utilisateur peut décider de se connecter pour accéder à la ressource protégée ou pour actualiser les informations obsolètes.

> [!NOTE]
> En raison d’un [problème connu](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) lié à la gestion des fenêtres contextuelles par le navigateur Internet Explorer, le code ci-dessus a recours aux méthodes `loginRedirect` et `acquireTokenRedirect` en cas d’utilisation de ce navigateur.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Appeler l’API Microsoft Graph à l’aide du jeton que vous venez d’obtenir

Ajoutez le code ci-après à votre fichier `index.html` entre les balises `<script></script>` :

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Plus d’informations sur l’envoi d’un appel REST à une API protégée

Dans l’exemple d’application créé par ce guide, la méthode `callMSGraph()` est utilisée pour envoyer une demande HTTP `GET` à une ressource protégée qui nécessite un jeton, puis pour retourner le contenu à l’appelant. Cette méthode ajoute le jeton acquis à l’*en-tête d’autorisation HTTP*. Dans l’exemple d’application créé par ce guide, la ressource est le point de terminaison *me* de l’API Microsoft Graph, qui affiche les informations de profil de l’utilisateur.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Ajouter une méthode pour déconnecter l’utilisateur

Ajoutez le code ci-après à votre fichier `index.html` entre les balises `<script></script>` :

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```
