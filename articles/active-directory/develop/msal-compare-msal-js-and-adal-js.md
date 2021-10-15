---
title: Migrer votre application JavaScript d’ADAL.js vers MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: Comment mettre à jour votre application JavaScrip existante pour utiliser la Bibliothèque d’authentification Microsoft (MSAL) pour l’authentification et l’autorisation au lieu de la Bibliothèque d’authentification du Répertoire actif (ADAL).
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/06/2021
ms.author: v-doeris
ms.custom: has-adal-ref
ms.openlocfilehash: 541200501fd9bd3cc1883283bc308445d0e4115e
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232240"
---
# <a name="how-to-migrate-a-javascript-app-from-adaljs-to-msaljs"></a>Comment migrer une application JavaScript d'ADAL.js vers MSAL.js

La [Bibliothèque d’authentification Microsoft pour JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) (MSAL.js, également appelée *msal-browser*) 2.x est la Bibliothèque d’authentification que nous recommandons d’utiliser avec les applications JavaScript sur la Plateforme d’identités Microsoft. Cet article met en évidence les modifications que vous devez effectuer pour migrer une application qui utilise ADAL.js pour utiliser MSAL.js 2. x

> [!NOTE]
> Nous vous recommandons vivement MSAL.js 2. x plutôt que MSAL.js 1. x. Le processus d’octroi de code d’authentification est plus sécurisé et permet aux applications à page unique de conserver une bonne expérience utilisateur malgré les mesures de confidentialité que les navigateurs tels que Safari ont implémentées pour bloquer les cookies tiers, entre autres avantages.

## <a name="prerequisites"></a>Configuration requise

- Vous devez paramétrer le **Type d’URL de réponse** / de la **Plateforme** sur **application de Page unique** sur le portail d’Inscription d’application (si vois possédez d’autres plateformes dans votre inscription d’application, tel que le **Web**, vous devez veiller à ce que les URI de redirection ne se chevauchent pas. Voir : [Restrictions de l’URI de redirection](./reply-url.md))
- Vous devez fournir des [polyremplissages](./msal-js-use-ie-browser.md) pour les fonctionnalités ES6 sur lesquelles s’appuie MSAL.js (par exemple, les promesses) afin d’exécuter vos applications sur **Internet Explorer**
- Assurez-vous d’avoir migré vos applications Azure AD vers le [point de terminaison v2](../azuread-dev/azure-ad-endpoint-comparison.md), si ce n’est déjà fait

## <a name="install-and-import-msal"></a>Installer et importer des packages

Il existe deux façons d’installer la bibliothèque MSAL.js 2. x :

### <a name="via-npm"></a>Via NPM :

```console
npm install @azure/msal-browser
```

Ensuite, en fonction de votre système de module, importez-la comme indiqué ci-dessous :

```javascript
import * as msal from "@azure/msal-browser"; // ESM

const msal = require('@azure/msal-browser'); // CommonJS
```

### <a name="via-cdn"></a>Via CDN :

Chargez le script dans la section d’en-tête de votre document HTML :

```html
<!DOCTYPE html>
<html>
  <head>
    <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js"></script>
  </head>
</html>
```

Pour obtenir d’autres liens CDN et les meilleures pratiques lors de l’utilisation de CDN, consultez : [utilisation de CDN](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md)

## <a name="initialize-msal"></a>Initialiser MSAL

Dans ADAL.js, vous instanciez la classe [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) qui expose ensuite les méthodes qui permettent l’authentification (`login`, `acquireTokenPopup` etc.). Cet objet sert de représentation de la connexion de votre application au serveur d’autorisation ou au fournisseur d’identité. Lors de l’initialisation, le seul paramètre obligatoire est le **clientId** :

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);
```

IDans ADAL.js, vous instanciez la classe [PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) à la place. A l’instar d’ADAL.js, le constructeur attend un [objet de configuration](#configure-msal) qui contient le `clientId` paramètre au minimum. En savoir plus : [Initialiser MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/initialization.md)

```javascript
const msalConfig = {
  auth: {
      clientId: 'YOUR_CLIENT_ID'
  }
};

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

Dans ADAL.js et MSAL.js, l’URI d’autorité est défini par défaut sur `https://login.microsoftonline.com/common` si vous ne le spécifiez pas.

> [!NOTE]
> Si vous utilisez l’autorité `https://login.microsoftonline.com/common` dans v2.0, vous autorisez les utilisateurs à se connecter à n’importe quelle organisation Azure AD ou à un compte personnel Microsoft (MSA). Dans MSAL.js, si vous voulez limiter la connexion à un compte Azure AD (même comportement qu’avec ADAL.js), utilisez `https://login.microsoftonline.com/organizations` à la place.

## <a name="configure-msal"></a>Configurer MSAL

Certaines [options de configuration dans ADAL.js](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context) utilisées lors de l’initialisation du [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) sont dépréciées dans MSAL.js, bien que de nouvelles y soient introduites. Consultez la [liste complète des options disponibles](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md). Surtout, la plupart de ces options, à l’exception de `clientId`, peuvent être remplacées lors de l’acquisition de jetons, ce qui vous permet de les définir en fonction de la *demande*. Par exemple, vous pouvez utiliser une **URI d’autorité** ou **URI de redirection** différente de celle que vous avez définie à l’initialisation lors de l’acquisition de jetons.

En outre, vous n’avez plus besoin de spécifier l’expérience de connexion (par exemple, si vous utilisez des fenêtres contextuelles ou si vous redirigez la page) via les options de configuration. A la place, `MSAL.js` expose les méthodes `loginPopup` et `loginRedirect` dans l’instance `PublicClientApplication`.

## <a name="enable-logging"></a>Activation de la journalisation

Dans ADAL.js, vous configurez la journalisation séparément à n’importe quel emplacement dans votre code :

```javascript
window.config = {
  clientId: "YOUR_CLIENT_ID"
};

var authContext = new AuthenticationContext(config);

var Logging = {
  level: 3,
  log: function (message) {
      console.log(message);
  },
  piiLoggingEnabled: false
};


authContext.log(Logging)
```

Dans MSAL.js, la journalisation fait partie des options de configuration et est créée pendant l’initialisation de `PublicClientApplication` :

```javascript
const msalConfig = {
  auth: {
      // authentication related parameters
  },
  cache: {
      // cache related parameters
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
}

const msalInstance = new msal.PublicClientApplication(msalConfig);
```

## <a name="switch-to-msal-api"></a>Basculer vers l’API MSAL

La plupart des méthodes publiques dans ADAL.js ont des équivalents dans MSAL.js :

| ADAL                                | MSAL                              | Notes                                        |
|-------------------------------------|-----------------------------------|----------------------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | Renommé et attend désormais un objet [account](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo) |
| `acquireTokenPopup`                 | `acquireTokenPopup`               | Maintenant asynchrone et retourne une promesse              |
| `acquireTokenRedirect`              | `acquireTokenRedirect`            | Maintenant asynchrone et retourne une promesse              |
| `handleWindowCallback`              | `handleRedirectPromise`           | Requise en cas d’utilisation de l’expérience de redirection          |
| `getCachedUser`                     | `getAllAccounts`                  | Renommée et retourne maintenant un tableau de comptes.|

D’autres ont été dépréciées, tandis que MSAL.js offre de nouvelles méthodes :

| ADAL                              | MSAL                            | Notes                                            |
|-----------------------------------|---------------------------------|--------------------------------------------------|
| `login`                           | N/A                             | Action déconseillée. Utilisez `loginPopup` ou `loginRedirect`  |
| `logOut`                          | N/A                             | Action déconseillée. Utilisez `logoutPopup` ou `logoutRedirect`|
| N/A                               | `loginPopup`                    |                                                  |
| N/A                               | `loginRedirect`                 |                                                  |
| N/A                               | `logoutPopup`                   |                                                  |
| N/A                               | `logoutRedirect`                |                                                  |
| N/A                               | `getAccountByHomeId`            | Filtre les comptes par ID de famille (OID + ID de locataire)    |
| N/A                               | `getAccountLocalId`             | Filtre les comptes par ID local (utile pour ADFS)   |
| N/A                               | `getAccountUsername`            | Filtre les comptes par nom d’utilisateur (s’il existe)         |

En outre, comme MSAL.js est implémentée dans TypeScript contrairement à ADAL.js, elle expose différents types et interfaces que vous pouvez utiliser dans vos projets. Pour plus d’informations, consultez les [informations de référence sur l’API MSAL.js](https://azuread.github.io/microsoft-authentication-library-for-js/ref/).

## <a name="use-scopes-instead-of-resources"></a>Utiliser des étendues au lieu de ressources

L’accès aux ressources est une différence importante entre les points de terminaison Azure AD **v1.0** vs. **v2.0**. Lors de l’utilisation d’ADAL.js avec le point de terminaison **v1.0**, vous devez d’abord inscrire une autorisation sur le portail d’inscription d’application, puis demander un jeton d’accès pour une ressource (par exemple, Microsoft Graph) comme indiqué ci-dessous :

```javascript
authContext.acquireTokenRedirect("https://graph.microsoft.com", function (error, token) {
  // do something with the access token
});
```

MSAL.js prend en charge les points de terminaison **v1.0** et **v2.0**. Le point de terminaison **v2.0** utilise un modèle *centré sur l’étendue* pour accéder aux ressources. Ainsi, lorsque vous demandez un jeton d’accès pour une ressource, vous devez également spécifier l’étendue de cette ressource :

```javascript
msalInstance.acquireTokenRedirect({
  scopes: ["https://graph.microsoft.com/User.Read"]
});
```

L’un des avantages du modèle centré sur l’étendue est la possibilité d’utiliser des *étendues dynamiques*. Lorsque vous génériez des applications à l'aide de la version 1.0, vous deviez enregistrer le jeu complet d'autorisations (appelées *étendues statiques*) exigé par l'application pour que l'utilisateur donne son consentement au moment de la connexion. Dans la version 2.0, vous pouvez utiliser le paramètre scope pour demander les autorisations au moment où vous le souhaitez (d’où le terme d’*étendues dynamiques*). Cela permet à l'utilisateur de fournir un **consentement incrémentiel** aux étendues. Par conséquent, si au début vous souhaitez juste que l'utilisateur se connecte à votre application et que vous n'avez besoin d'aucun type d'accès, c'est possible. Si, par la suite, vous devez avoir la possibilité de lire le calendrier de l'utilisateur, vous pouvez alors demander l'étendue de celui-ci dans les méthodes acquireToken et obtenir le consentement de l'utilisateur. Pour plus d’informations, consultez [Ressources et étendues](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)

## <a name="use-promises-instead-of-callbacks"></a>Utilisation de promesses au lieu de rappels

Dans ADAL.js, les rappels sont utilisés pour toute opération une fois que l’authentification a réussi et qu’une réponse est obtenue :

```javascript
authContext.acquireTokenPopup(resource, extraQueryParameter, claims, function (error, token) {
  // do something with the access token
});
```

Dans MSAL.js, des promesses sont utilisées à la place :

```javascript
msalInstance.acquireTokenPopup({
      scopes: ["User.Read"] // shorthand for https://graph.microsoft.com/User.Read
  }).then((response) => {
      // do something with the auth response
  }).catch((error) => {
      // handle errors
  });
```

Vous pouvez également utiliser la syntaxe **async/await** qui est fournie avec ES8 :

```javascript
const getAccessToken = async() => {
  try {
      const authResponse = await msalInstance.acquireTokenPopup({
          scopes: ["User.Read"]
      });
  } catch (error) {
      // handle errors
  }
}
```

## <a name="cache-and-retrieve-tokens"></a>Mise en cache et récupération de jetons

Comme ADAL.js, MSAL.js met en cache les jetons et autres artefacts d’authentification dans le stockage du navigateur, à l’aide de l'[API de Stockage Web](https://developer.mozilla.org/docs/Web/API/Web_Storage_API). Nous vous conseillons d’utiliser l’option `sessionStorage` (consultez : [configuration](#configure-msal)) car elle est davantage sécurisée pour le stockage des jetons obtenus par vos utilisateurs. Cependant, `localStorage` vous accordera l’[Authentification unique](./msal-js-sso.md) sur les onglets et sessions utilisateur.

Important, vous n’êtes pas censé accéder au cache directement. Au lieu de cela, vous devez utiliser une API de MSAL.js appropriée pour récupérer les artefacts d’authentification, comme les jetons d’accès ou les comptes d’utilisateur.

## <a name="renew-tokens-with-refresh-tokens"></a>Renouveler des jetons avec des jetons d’actualisation

ADAL.js utilise le [Flux implicite OAuth 2.0](./v2-oauth2-implicit-grant-flow.md), lequel ne retourne pas les jetons d’actualisation pour des raisons de sécurité (les jetons d’actualisation ont une durée de vie plus longue que les jetons d’accès et sont donc plus dangereux entre les mains d’acteurs malveillants). Par conséquent, ADAL.js effectue le renouvellement de jeton à l’aide d’un Iframe masqué afin que l’utilisateur ne soit pas invité à s’authentifier à plusieurs reprises.

Grâce au flux de code d’authentification avec prise en charge de PKCE, les applications qui utilisent MSAL.js 2. x obtiennent des jetons d’actualisation, ainsi que des ID et des jetons d’accès, qui peuvent être utilisés pour leur renouvellement. L’utilisation de jetons d’actualisation est abstraite, et les développeurs ne sont pas censés créer une logique autour de ces derniers. Au lieu de cela, MSAL gère indépendamment le renouvellement des jetons à l’aide de jetons d’actualisation. Votre cache de jetons précédent avec ADAL.js ne pourra pas être transféré vers MSAL.js, car le schéma du cache de jetons a changé et est incompatible avec le schéma utilisé dans ADAL.js.

## <a name="handle-errors-and-exceptions"></a>Gérer les erreurs et exceptions

Lorsque vous utilisez MSAL.js, le type d’erreur le plus courant que vous pouvez rencontrer est l'erreur`interaction_in_progress`. Cette erreur est générée lorsqu’une API interactive (`loginPopup`, `loginRedirect`, `acquireTokenPopup`, `acquireTokenRedirect`) est appelée alors qu’une autre API interactive est toujours en cours. Les API `login*` et `acquireToken*` sont *asynchrones*, vous devrez donc vous assurer de la résolution des promesses obtenues avant d’en invoquer une autre.

Une autre erreur courante est `interaction_required`. Cette erreur est souvent résolue en lançant simplement une invite d’acquisition de jeton interactive. Par exemple, l’API Web à laquelle vous tentez d’accéder peut posséder une stratégie d’[accès conditionnel](../conditional-access/overview.md), nécessitant que l’utilisateur réalise une [authentification multifacteur](../authentication/concept-mfa-howitworks.md) (MFA). Dans ce cas, traiter l’`interaction_required` erreur en déclenchant `acquireTokenPopup` ou `acquireTokenRedirect` invitera l’utilisateur à réaliser une MFA, en lui permettant de la compléter.

Une autre erreur courante que vous pouvez rencontrer est `consent_required`, qui se produit lorsque les autorisations requises pour obtenir un jeton d’accès pour une ressource protégée ne sont pas consenties par l’utilisateur. Comme pour `interaction_required`, la solution pour l’erreur `consent_required` est souvent d’initier une demande d’acquisition de jetons interactive, en utilisant soit `acquireTokenPopup` soit `acquireTokenRedirect`.

En savoir plus : [erreurs MSAL.js courantes et comment les résoudre](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/errors.md)

## <a name="use-the-events-api"></a>Utiliser l’API d’événements

MSAL.js (>=v2.4) introduit une API d’événements que vous pouvez utiliser dans vos applications. Ces événements sont liés au processus d’authentification et à ce que MSAL fait à tout moment et peuvent être utilisés pour mettre à jour l’interface utilisateur, afficher les messages d’erreur, vérifier si une interaction est en cours, et ainsi de suite. Par exemple, voici un rappel d’événement qui sera appelé lorsque le processus de connexion échoue pour une raison quelconque :

```javascript
const callbackId = msalInstance.addEventCallback((message) => {
  // Update UI or interact with EventMessage here
  if (message.eventType === EventType.LOGIN_FAILURE) {
      if (message.error instanceof AuthError) {
          // Do something with the error
      }
    }
});
```

Pour des performances, il est important d’annuler l’inscription des rappels d’événements lorsqu’ils ne sont plus nécessaires. En savoir plus : [API d’événements MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/events.md)

## <a name="handle-multiple-accounts"></a>Gestion de plusieurs comptes

ADAL.js détient le concept *utilisateur* pour représenter l’entité authentifiée actuelle. MSAL.js remplace les *utilisateurs* avec plusieurs *comptes*, du fait qu’un utilisateur peut être associé à plus d’un compte. Cela signifie également que vous devez maintenant contrôler plusieurs comptes et choisir celui qui convient. L’extrait de code ci-dessous illustre ce processus :

```javascript
let homeAccountId = null; // Initialize global accountId (can also be localAccountId or username) used for account lookup later, ideally stored in app state

// This callback is passed into `acquireTokenPopup` and `acquireTokenRedirect` to handle the interactive auth response
function handleResponse(resp) {
  if (resp !== null) {
      homeAccountId = resp.account.homeAccountId; // alternatively: resp.account.homeAccountId or resp.account.username
  } else {
      const currentAccounts = myMSALObj.getAllAccounts();
      if (currentAccounts.length < 1) { // No cached accounts
          return;
      } else if (currentAccounts.length > 1) { // Multiple account scenario
          // Add account selection logic here
      } else if (currentAccounts.length === 1) {
          homeAccountId = currentAccounts[0].homeAccountId; // Single account scenario
      }
  }
}
```

Pour plus d’informations, consultez [Comptes dans MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/accounts.md)

## <a name="use-the-wrappers-libraries"></a>Utilisation des bibliothèques wrappers

Si vous développez des infrastructures Angular et React, vous pouvez utiliser respectivement [MSAL Angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) et [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react). Ces wrappers exposent la même API publique que MSAL.js tout en offrant des méthodes et des composants spécifiques à l’infrastructure qui peuvent simplifier les processus d’authentification et d’acquisition de jetons.

## <a name="run-the-app"></a>Exécuter l’application

Une fois vos modifications effectuées, exécutez l’application et testez votre scénario d’authentification :

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>Exemple : Sécurisation des applications web à l’aide d’ADAL Node et de MSAL Node

Les extraits de code ci-dessous illustrent le code minimal requis pour une application à page unique qui authentifie les utilisateurs grâce à la plateforme d’identités Microsoft et obtient un jeton d’accès pour Microsoft Graph en utilisant d’abord ADAL.js puis MSAL.js :

<table>
<tr><td> Utilisation d’ADAL.js </td><td> Utilisation de MSAL.js </td></tr>
<tr>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script 
    type="text/javascript"
    src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.18/js/adal.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>

    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    var authContext = new AuthenticationContext({
        instance: 'https://login.microsoftonline.com/',
        clientId: "ENTER_CLIENT_ID",
        tenant: "ENTER_TENANT_ID",
        cacheLocation: "sessionStorage",
        redirectUri: "http://localhost:3000",
        popUp: true,
        callback: function (errorDesc, token, error, tokenType) {
            console.log('Hello ' + authContext.getCachedUser().profile.upn)

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        }
    });

    authContext.log({
        level: 3,
        log: function (message) {
            console.log(message);
        },
        piiLoggingEnabled: false
    });

    loginButton.addEventListener('click', function () {
        authContext.login();
    });

    logoutButton.addEventListener('click', function () {
        authContext.logOut();
    });

    tokenButton.addEventListener('click', () => {
        authContext.acquireTokenPopup(
            "https://graph.microsoft.com", 
            null, null, 
            function (error, token) {
                console.log(error, token);
            }
        )
    });
  </script>
</body>

</html>

```

</td>
<td>

```html

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <script 
    type="text/javascript" 
    src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js">
  </script>
</head>

<div>
  <button id="loginButton">Login</button>
  <button id="logoutButton" style="visibility: hidden;">Logout</button>
  <button id="tokenButton" style="visibility: hidden;">Get Token</button>
</div>

<body>
  <script>
    const loginButton = document.getElementById("loginButton");
    const logoutButton = document.getElementById("logoutButton");
    const tokenButton = document.getElementById("tokenButton");

    const pca = new msal.PublicClientApplication({
        auth: {
            clientId: "ENTER_CLIENT_ID",
            authority: "https://login.microsoftonline.com/ENTER_TENANT_ID",
            redirectUri: "http://localhost:3000",
        },
        cache: {
            cacheLocation: "sessionStorage"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    });

    loginButton.addEventListener('click', () => {
        pca.loginPopup().then((response) => {
            console.log(`Hello ${response.account.username}!`);

            loginButton.style.visibility = "hidden";
            logoutButton.style.visibility = "visible";
            tokenButton.style.visibility = "visible";
        })
    });

    logoutButton.addEventListener('click', () => {
        pca.logoutPopup().then((response) => {
            window.location.reload();
        })
    });

    tokenButton.addEventListener('click', () => {
        pca.acquireTokenPopup({
            scopes: ["User.Read"]
        }).then((response) => {
            console.log(response);
        })
    });
  </script>
</body>

</html>

```

</td>
</tr>
</table>

## <a name="next-steps"></a>Étapes suivantes

- [Informations de référence sur l’API MSAL.js](https://azuread.github.io/microsoft-authentication-library-for-js/ref/)
- [Exemples de code MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples)
