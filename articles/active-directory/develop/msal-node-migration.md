---
title: Migration de votre application Node.js d’ADAL vers MSAL | Azure
titleSuffix: Microsoft identity platform
description: Comment mettre à jour votre application Node.js existante pour utiliser la bibliothèque d’authentification Microsoft (MSAL) pour l’authentification et l’autorisation au lieu de la bibliothèque d’authentification Active Directory (ADAL).
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/26/2021
ms.author: v-doeris
ms.openlocfilehash: 0fbcd0437488631d8bd4b34d67a28bda81f2a6e9
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539893"
---
# <a name="how-to-migrate-a-nodejs-app-from-adal-to-msal"></a>Migration d’une application Node.js d’ADAL vers MSAL

La [bibliothèque d’authentification Microsoft pour Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) (MSAL Node) est désormais le kit de développement logiciel (SDK) recommandé pour l’activation de l’authentification et de l’autorisation pour vos applications inscrites sur la plateforme d’identité Microsoft. Cet article décrit les étapes importantes à suivre pour migrer vos applications de la bibliothèque d’authentification Active Directory pour Node (ADAL Node) vers MSAL Node.

## <a name="prerequisites"></a>Prérequis

- Node version 10, 12 ou 14. Consultez la [note sur la prise en charge des versions](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node#node-version-support)

## <a name="update-app-registration-settings"></a>Mettre à jour les paramètres d’inscription d’application

Lorsque de votre travail sur ADAL Node, vous utilisiez probablement le **point de terminaison Azure AD v1.0**. Les applications qui migrent d’ADAL vers MSAL doivent également envisager de basculer vers le **point de terminaison Azure AD v2.0**.

1. Vérifiez les [différences entre les points de terminaison v1 et v2](../azuread-dev/azure-ad-endpoint-comparison.md)
1. Mettez à jour, si nécessaire, vos inscriptions d’applications existantes en conséquence.

> [!NOTE]
> Pour garantir la compatibilité descendante, MSAL Node prend en charge les points de terminaison v1.0 et v2.0.

## <a name="install-and-import-msal"></a>Installer et importer des packages

1. Installez le package de MSAL Node via NPM :

```console
npm install @azure/msal-node
```

1. Après cela, importez MSAL Node dans votre code :

```javascript
const msal = require('@azure/msal-node');
```

1. Enfin, désinstallez le package d’ADAL Node et supprimez toutes les références dans votre code :

```console
npm uninstall adal-node
```

## <a name="initialize-msal"></a>Initialiser MSAL

Dans ADAL Node, vous initialisez un objet `AuthenticationContext`, qui expose ensuite les méthodes que vous pouvez utiliser dans différents flux d’authentification (par exemple, `acquireTokenWithAuthorizationCode` pour les applications web). Lors de l’initialisation, le seul paramètre obligatoire est l’**URI d’autorité** :

```javascript
var adal = require('adal-node');

var authorityURI = "https://login.microsoftonline.com/common";
var authenticationContex = new adal.AuthenticationContext(authorityURI);
```

Dans MSAL Node, vous avez deux possibilités : si vous générez une application mobile ou une application de bureau, vous instanciez un objet `PublicClientApplication`. Le constructeur attend un [objet de configuration](#configure-msal) qui contient le paramètre `clientId` au minimum. MSAL définit par défaut l’URI d’autorité à `https://login.microsoftonline.com/common` si vous ne le spécifiez pas.

```javascript
const msal = require('@azure/msal-node');

const pca = new msal.PublicClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID"
        }
    });
```

> [!NOTE]
> Si vous utilisez l’autorité `https://login.microsoftonline.com/common` dans v2.0, vous autorisez les utilisateurs à se connecter à n’importe quelle organisation Azure AD ou à un compte personnel Microsoft (MSA). Dans MSAL Node, si vous voulez limiter la connexion à un compte Azure AD (même comportement qu’avec ADAL Node), utilisez `https://login.microsoftonline.com/organizations` à la place.

En revanche, si vous générez une application web ou une application démon, vous instanciez un objet `ConfidentialClientApplication`. Avec ces applications, vous devez également fournir des *informations d’identification de client*, comme une clé secrète client ou un certificat :

```javascript
const msal = require('@azure/msal-node');

const cca = new msal.ConfidentialClientApplication({
        auth: {
            clientId = "YOUR_CLIENT_ID",
            clientSecret = "YOUR_CLIENT_SECRET"
        }
    });
```

`PublicClientApplication` et `ConfidentialClientApplication`, contrairement `AuthenticationContext` d’ADAL, sont liés à un ID client. Cela signifie que si vous avez différents ID client que vous souhaitez utiliser dans votre application, vous devez instancier une nouvelle instance de MSAL pour chacun d’entre eux. Pour plus d’informations : [Initialisation de MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

## <a name="configure-msal"></a>Configurer MSAL

Lors de la création d’applications sur la plateforme d’identité Microsoft, votre application contiendra de nombreux paramètres liés à l’authentification. Dans ADAL Node, l’objet `AuthenticationContext` a un nombre limité de paramètres de configuration avec lesquels vous pouvez l’instancier, tandis que les paramètres restants résident librement dans votre code (par exemple, *clientSecret*) :

```javascript
var adal = require('adal-node');

var authority = "https://login.microsoftonline.com/YOUR_TENANT_ID"
var validateAuthority = true,
var cache = null;

var authenticationContext = new adal.AuthenticationContext(authority, validateAuthority, cache);
```

- `authority` : une URL qui identifie une autorité de jeton
- `validateAuthority` : une fonctionnalité qui empêche votre code de demander des jetons à une autorité potentiellement malveillante
- `cache` : définit le cache de jeton utilisé par cette instance d’AuthenticationContext.  Si ce paramètre n’est pas défini, une valeur par défaut est utilisée dans le cache mémoire

En revanche, MSAL Node utilise un objet de configuration de type [Configuration](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html#configuration). Il contient les propriétés suivantes :

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "YOUR_CLIENT_ID",
        authority: "https://login.microsoftonline.com/YOUR_TENANT_ID",
        clientSecret: "YOUR_TENANT_ID",
        knownAuthorities: [], 
    },
    cache: {
        // your implementation of caching
    },
    system: {
        loggerOptions: { /** logging related options */ }
    }
}


const cca = new msal.ConfidentialClientApplication(msalConfig);
```

Différence notable, MSAL n’a pas d’indicateur pour désactiver la validation de l’autorité et les autorités sont toujours validées par défaut. MSAL compare votre autorité demandée à une liste d’autorités connues de Microsoft ou à la liste des autorités que vous avez spécifiées dans votre configuration. Pour plus d’informations : [Options de configuration](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)

## <a name="enable-logging"></a>Activation de la journalisation

Dans ADAL Node, vous configurez la journalisation séparément à n’importe quel emplacement dans votre code :

```javascript
var adal = require('adal-node');

//PII or OII logging disabled. Default Logger does not capture any PII or OII.
adal.logging.setLoggingOptions({
  log: function (level, message, error) {
    console.log(message);

    if (error) {
        console.log(error);
    }
  },
  level: logging.LOGGING_LEVEL.VERBOSE, // provide the logging level
  loggingWithPII: false  // Determine if you want to log personal identification information. The default value is false.
});
```

Dans MSAL Node, la journalisation fait partie des options de configuration et est créée avec l’initialisation de l’instance MSAL Node :

```javascript
const msal = require('@azure/msal-node');

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

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

## <a name="use-scopes-instead-of-resources"></a>Utiliser des étendues au lieu de ressources

L’accès aux ressources est une différence importante entre les points de terminaison v1.0 et v2.0. Dans ADAL Node, vous devez d’abord inscrire une autorisation sur le portail d’inscription des applications, puis demander un jeton d’accès pour une ressource (par exemple, Microsoft Graph) comme indiqué ci-dessous :

```javascript
authenticationContext.acquireTokenWithAuthorizationCode(
    req.query.code,
    redirectUri,
    resource, // e.g. 'https://graph.microsoft.com'
    clientId,
    clientSecret,
    function (err, response) {
        // do something with the authentication response
    }
);
```

MSAL Node prend en charge les points de terminaison **v1.0** et **v2.0**. Le point de terminaison v2.0 utilise un modèle centré sur l’*étendue* pour accéder aux ressources. Ainsi, lorsque vous demandez un jeton d’accès pour une ressource, vous devez également spécifier l’étendue de cette ressource :

```javascript
const tokenRequest = {
    code: req.query.code,
    scopes: ["https://graph.microsoft.com/User.Read"],
    redirectUri: REDIRECT_URI,
};

pca.acquireTokenByCode(tokenRequest).then((response) => {
    // do something with the authentication response
}).catch((error) => {
    console.log(error);
});
```

L’un des avantages du modèle centré sur l’étendue est la possibilité d’utiliser des *étendues dynamiques*. Lorsque vous génériez des applications à l'aide de la version 1.0, vous deviez enregistrer le jeu complet d'autorisations (appelées *étendues statiques*) exigé par l'application pour que l'utilisateur donne son consentement au moment de la connexion. Dans la version 2.0, vous pouvez utiliser le paramètre scope pour demander les autorisations au moment où vous le souhaitez (d’où le terme d’*étendues dynamiques*). Cela permet à l'utilisateur de fournir un **consentement incrémentiel** aux étendues. Par conséquent, si au début vous souhaitez juste que l'utilisateur se connecte à votre application et que vous n'avez besoin d'aucun type d'accès, c'est possible. Si, par la suite, vous devez avoir la possibilité de lire le calendrier de l'utilisateur, vous pouvez alors demander l'étendue de celui-ci dans les méthodes acquireToken et obtenir le consentement de l'utilisateur. Pour plus d’informations, consultez [Ressources et étendues](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/resources-and-scopes.md)

## <a name="switch-to-msal-api"></a>Basculer vers l’API MSAL

La plupart des méthodes publiques d’ADAL Node ont des équivalents dans MSAL Node :

| ADAL                                | MSAL                              | Notes                             |
|-------------------------------------|-----------------------------------|-----------------------------------|
| `acquireToken`                      | `acquireTokenSilent`              | Renommé et attend désormais un objet [account](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_common.html#accountinfo) |
| `acquireTokenWithAuthorizationCode` | `acquireByAuthorizationCode`      |                                   |
| `acquireTokenWithClientCredentials` | `acquireTokenByClientCredential` |                                   |
| `acquireTokenWithRefreshToken`      | `acquireTokenByRefreshToken`      |                                   |
| `acquireTokenWithDeviceCode`        | `acquireTokenByDeviceCode`        | Simplifie maintenant l’acquisition du code utilisateur (voir ci-dessous) |
| `acquireTokenWithUsernamePassword`  | `acquireTokenByUsernamePassword`  |                                   |

Toutefois, certaines méthodes d’ADAL Node sont dépréciées, tandis que MSAL Node en offre de nouvelles :

| ADAL                              | MSAL                            | Notes                             |
|-----------------------------------|---------------------------------|-----------------------------------|
| `acquireUserCode`                   | N/A                             | Fusionné avec `acquireTokeByDeviceCode` (voir ci-dessus)|
| N/A                               | `acquireTokenOnBehalfOf`          | Nouvelle méthode qui simplifie le [flux OBO](./v2-oauth2-on-behalf-of-flow.md) |
| `acquireTokenWithClientCertificate` | N/A                             | N’est plus nécessaire, car les certificats sont attribués au cours de l’initialisation (voir [options de configuration](#configure-msal)) |
| N/A                               | `getAuthCodeUrl`                  | Nouvelle méthode qui simplifie la construction d’une URL de [point de terminaison d’autorisation](./active-directory-v2-protocols.md#endpoints) |

## <a name="use-promises-instead-of-callbacks"></a>Utilisation de promesses au lieu de rappels

Dans ADAL Node, les rappels sont utilisés pour toute opération une fois que l’authentification a réussi et qu’une réponse est obtenue :

```javascript
var context = new AuthenticationContext(authorityUrl, validateAuthority);

context.acquireTokenWithClientCredentials(resource, clientId, clientSecret, function(err, response) {
    if (err) {
        console.log(err);
    } else {
        // do something with the authentication response
    }
});
```

Dans MSAL Node, des promesses sont utilisées à la place :

```javascript
    const cca = new msal.ConfidentialClientApplication(msalConfig);

    cca.acquireTokenByClientCredential(tokenRequest).then((response) => {
        // do something with the authentication response
    }).catch((error) => {
        console.log(error);
    });
```

Vous pouvez également utiliser la syntaxe **async/await** qui est fournie avec ES8 :

```javascript
    try {
        const authResponse = await cca.acquireTokenByCode(tokenRequest);
    } catch (error) {
        console.log(error);
    }
```

## <a name="enable-token-caching"></a>Activer la mise en cache des jetons

Dans ADAL Node, vous aviez la possibilité d’importer un cache de jeton en mémoire. Le cache de jeton est utilisé en tant que paramètre lors de l’initialisation d’un objet `AuthenticationContext` :

```javascript
var MemoryCache = require('adal-node/lib/memory-cache');

var cache = new MemoryCache();
var authorityURI = "https://login.microsoftonline.com/common";

var context = new AuthenticationContext(authorityURI, true, cache);
```

MSAL Node utilise un cache de jeton en mémoire par défaut. Vous n’avez pas besoin de l’importer explicitement. Il est exposé dans le cadre des objets `ConfidentialClientApplication` et `PublicClientApplication`.

```javascript
const msalTokenCache = publicClientApplication.getTokenCache();
```

Vous pouvez également écrire votre cache sur disque en fournissant votre propre **plug-in de cache**. Le plug-in du cache doit implémenter l’interface [ICachePlugin](https://azuread.github.io/microsoft-authentication-library-for-js/ref/interfaces/_azure_msal_common.icacheplugin.html). Comme la journalisation, la mise en cache fait partie des options de configuration et est créée avec l’initialisation de l’instance MSAL Node :

```javascript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        // authentication related parameters 
    },
    cache: {
        cachePlugin // your implementation of cache plugin
    },
    system: {
        // logging related options 
    }
}

const msalInstance = new ConfidentialClientApplication(msalConfig);
```

Un exemple de plug-in de cache peut être implémenté comme indiqué ci-dessous :

```javascript
const fs = require('fs');

// Call back APIs which automatically write and read into a .json file - example implementation
const beforeCacheAccess = async (cacheContext) => {
    cacheContext.tokenCache.deserialize(await fs.readFile(cachePath, "utf-8"));
};

const afterCacheAccess = async (cacheContext) => {
    if(cacheContext.cacheHasChanged) {
        await fs.writeFile(cachePath, cacheContext.tokenCache.serialize());
    }
};

// Cache Plugin
const cachePlugin = {
    beforeCacheAccess,
    afterCacheAccess
};
```

Si vous développez des [applications clientes publiques](./msal-client-applications.md), comme des applications de bureau, les [extensions d’authentification Microsoft pour Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions) offrent des mécanismes sécurisés permettant aux applications clientes d’effectuer la sérialisation et la persistance du cache de jetons multiplateforme. Les plateformes prises en charge sont Windows, Mac et Linux.

> [!NOTE]
> Les [extensions d’authentification Microsoft pour Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/extensions/msal-node-extensions) ne sont **pas** recommandées pour les applications web, car cela peut entraîner des problèmes de mise à l’échelle et de performances. Au lieu de cela, les applications web sont recommandées pour rendre le cache persistant dans la session.

## <a name="remove-logic-around-refresh-tokens"></a>Suppression de la logique entourant les jetons d’actualisation

Dans ADAL Node, les jetons d’actualisation (RT) ont été exposés, ce qui vous permet de développer des solutions autour de l’utilisation de ces jetons en les mettant en cache et en utilisant la méthode `acquireTokenWithRefreshToken`. Scénarios classiques dans lesquels les RT sont particulièrement pertinents :

- Services durables qui exécutent des actions, notamment l’actualisation des tableaux de bord pour le compte des utilisateurs alors que ces utilisateurs ne sont plus connectés.
- Scénarios WebFarm pour permettre au client d’apporter le RT au service web (la mise en cache est effectuée côté client, le cookie est chiffré, et non côté serveur).

Pour des raisons de sécurité, MSAL Node n'expose pas les jetons d'actualisation. C’est MSAL qui gère l’actualisation des jetons pour vous. Par conséquent, vous n’avez plus besoin de créer une logique pour cela. Toutefois, si vous devez migrer et utiliser vos jetons d’actualisation précédemment acquis, MSAL Node offre `acquireTokenByRefreshToken`, qui est l’équivalent de la méthode `acquireTokenWithRefreshToken` d’ADAL Node.

## <a name="run-the-app"></a>Exécuter l’application

Une fois vos modifications effectuées, exécutez l’application et testez votre scénario d’authentification :

```console
npm start
```

## <a name="example-securing-web-apps-with-adal-node-vs-msal-node"></a>Exemple : Sécurisation des applications web à l’aide d’ADAL Node et de MSAL Node

L’extrait de code ci-dessous illustre une application web cliente confidentielle dans le framework Express.js. Elle effectue une connexion lorsqu’un utilisateur accède à l’itinéraire d’authentification `/auth`, acquiert un jeton d’accès pour Microsoft Graph via l’itinéraire `/redirect`, puis affiche le contenu de ce jeton.


<table>
<tr><td> Utiliser le nœud ADAL </td><td> Utiliser le nœud MSAL </td></tr>
<tr>
<td>

```javascript
// Import dependencies
var express = require('express');
var crypto = require('crypto');
var adal = require('adal-node');

// Authentication parameters
var clientId = 'Enter_the_Application_Id_Here';
var clientSecret = 'Enter_the_Client_Secret_Here';
var tenant = 'common';
var authorityUrl = 'https://login.microsoftonline.com/' + tenant;
var redirectUri = 'http://localhost:3000/redirect';
var resource = 'https://graph.microsoft.com';

// Configure logging
adal.Logging.setLoggingOptions({
    log: function (level, message, error) {
        console.log(message);
    },
    level: adal.Logging.LOGGING_LEVEL.VERBOSE,
    loggingWithPII: false
});

// Auth code request URL template
var templateAuthzUrl = 'https://login.microsoftonline.com/' 
    + tenant + '/oauth2/authorize?response_type=code&client_id=' 
    + clientId + '&redirect_uri=' + redirectUri 
    + '&state=<state>&resource=' + resource;

// Initialize express
var app = express();

// State variable persists throughout the app lifetime
app.locals.state = "";

app.get('/auth', function(req, res) {

    // Create a random string to use against XSRF
    crypto.randomBytes(48, function(ex, buf) {
        app.locals.state = buf.toString('base64')
            .replace(/\//g, '_')
            .replace(/\+/g, '-');
        
        // Construct auth code request URL
        var authorizationUrl = templateAuthzUrl
            .replace('<state>', app.locals.state);

        res.redirect(authorizationUrl);
    });
});

app.get('/redirect', function(req, res) {
    // Compare state parameter against XSRF
    if (app.locals.state !== req.query.state) {
        res.send('error: state does not match');
    }

    // Initialize an AuthenticationContext object
    var authenticationContext = 
        new adal.AuthenticationContext(authorityUrl);
    
    // Exchange auth code for tokens
    authenticationContext.acquireTokenWithAuthorizationCode(
        req.query.code, 
        redirectUri, 
        resource, 
        clientId, 
        clientSecret,
        function(err, response) {
            res.send(response);
        }
    );
});

app.listen(3000, function() { 
    console.log(`listening on port 3000!`); 
});
```

</td>
<td>

```javascript
// Import dependencies
const express = require("express");
const msal = require('@azure/msal-node');

// Authentication parameters
const config = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/common",
        clientSecret: "Enter_the_Client_Secret_Here"
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
};

const REDIRECT_URI = "http://localhost:3000/redirect";

// Initialize MSAL Node object using authentication parameters
const cca = new msal.ConfidentialClientApplication(config);

// Initialize express
const app = express();

app.get('/auth', (req, res) => {
    
    // Construct a request object for auth code
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Request auth code, then redirect
    cca.getAuthCodeUrl(authCodeUrlParameters)
        .then((response) => {
            res.redirect(response);
        }).catch((error) => res.send(error));
});

app.get('/redirect', (req, res) => {
    
    // Use the auth code in redirect request to construct
    // a token request object
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // Exchange the auth code for tokens
    cca.acquireTokenByCode(tokenRequest)
        .then((response) => {
            res.send(response);
        }).catch((error) => res.status(500).send(error));
});

app.listen(3000, () => 
    console.log(`listening on port 3000!`));
```

</td>
</tr>
</table>

## <a name="next-steps"></a>Étapes suivantes

- [Référence de l’API MSAL Node](https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal_node.html)
- [Exemples de code MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples)
