---
title: Activer l’authentification dans une application SPA à l’aide des blocs de construction Azure Active Directory B2C
description: Cet article traite des blocs de construction d’Azure Active Directory B2C pour la connexion et l’inscription des utilisateurs dans une application SPA.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: a42d8f356286e2936ca9d6bdd15151bbf9ef1064
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007331"
---
# <a name="enable-authentication-in-your-own-single-page-application-by-using-azure-ad-b2c"></a>Activer l’authentification dans votre propre application monopage en utilisant Azure Active Directory B2C

Cet article explique comment ajouter l’authentification Azure Active Directory B2C (Azure AD B2C) à votre propre application monopage (SPA). Découvrez comment créer une application SPA en utilisant la [bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js). 

Utilisez cet article avec l’article [Configuration de l’authentification dans un exemple d’application SPA](./configure-authentication-sample-spa-app.md), en remplaçant l’exemple d’application SPA par votre propre application SPA.

## <a name="overview"></a>Vue d'ensemble

Cet article utilise Node.js et [Express](https://expressjs.com/) pour créer une application web Node.js de base. Express est une infrastructure d’applications web Node.js flexible et minimale qui fournit un ensemble de fonctionnalités aux applications web et mobiles.

La bibliothèque d’authentification [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications SPA.

> [!TIP]
> L’intégralité du code MSAL.js s’exécute côté client. Vous pouvez remplacer le Node.js et le code côté serveur Express par d’autres solutions, telles que les langages de script .NET Core, Java et PHP (Hypertext Preprocesseur).

## <a name="prerequisites"></a>Configuration requise

Pour passer en revue les conditions préalables et les instructions d’intégration, consultez [Configurer l’authentification dans un exemple d’application SPA](configure-authentication-sample-spa-app.md).

## <a name="step-1-create-an-spa-app-project"></a>Étape 1 : créer un projet d’application SPA

Vous pouvez utiliser un projet d’application SPA existant ou en créer un. Pour créer un projet, procédez de la façon suivante :

1. Ouvrez un interpréteur de commandes, puis créez un répertoire (par exemple, *myApp*). Ce répertoire contient le code de votre application, l’interface utilisateur et les fichiers de configuration.

1. Accédez au répertoire que vous avez créé.

1. Utilisez la commande `npm init` pour créer un fichier `package.json` pour votre application. Cette commande vous invite à entrer des informations sur votre application (par exemple, le nom et la version de votre application, ainsi que le nom du point d’entrée initial, le fichier *index.js*). Exécutez la commande suivante et acceptez les valeurs par défaut :

```
npm init
```

## <a name="step-2-install-the-dependencies"></a>Étape 2 : installer les dépendances

Pour installer le package Express, dans votre interpréteur de commandes, exécutez la commande suivante :

```
npm install express
```

Pour localiser les fichiers statiques de l’application, le code côté serveur utilise le package [Path](https://www.npmjs.com/package/path). 

Pour installer le package Path, dans votre interpréteur de commandes, exécutez la commande suivante :

```
npm install path
```

## <a name="step-3-configure-your-web-server"></a>Étape 3 : configurer votre serveur web

Dans votre dossier *myApp*, créez un fichier nommé *index.js*, qui contient le code suivant :

```javascript
// Initialize express
const express = require('express');
const app = express();

// The port to listen to incoming HTTP requests
const port = 6420;

// Initialize path
const path = require('path');

// Set the front-end folder to serve public assets.
app.use(express.static('App'));

// Set up a route for the index.html
app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname + '/index.html'));
});

// Start the server, and listen for HTTP requests
app.listen(port, () => {
  console.log(`Listening on http://localhost:${port}`);
});
```

## <a name="step-4-create-the-spa-user-interface"></a>Étape 4 : créer l’interface utilisateur de SPA

Ajoutez le fichier `index.html` de l’application SAP. Ce fichier implémente une interface utilisateur qui est générée avec une infrastructure de démarrage et il importe des fichiers de script pour la configuration, l’authentification et les appels d’API web.

Les ressources référencées par le fichier *index.html* sont détaillées dans le tableau suivant : 

|Référence |Définition|
|---|---|
|Bibliothèque MSAL.js| [Chemin CDN](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/cdn-usage.md) de la bibliothèque JavaScript d’authentification MSAL.js.|
|[Feuille de style de démarrage](https://getbootstrap.com/) | Une infrastructure frontale gratuite pour un développement web plus rapide et plus facile. L’infrastructure comprend des modèles de conception basés sur HTML et CSS. |
|[`policies.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/policies.js) | Contient les stratégies personnalisées Azure AD B2C et les flux utilisateur. |
|[`authConfig.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authConfig.js) | Contient les paramètres de configuration de l’authentification.|
|[`authRedirect.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/authRedirect.js) | Contient la logique d'authentification. |
|[`apiConfig.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/apiConfig.js) | Contient les étendues de l’API web et l’emplacement du point de terminaison de l’API. |
|[`api.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/api.js) | Définit la méthode à utiliser pour appeler votre API et gérer sa réponse.|
|[`ui.js`](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/blob/main/App/ui.js) | Contrôle les éléments d’interface utilisateur. |
| | |

Pour afficher le fichier d’index SPA, dans le dossier *myApp*, créez un fichier nommé *index.html*, qui contient l’extrait HTML suivant.

```html
<!DOCTYPE html>
<html>
    <head>
        <title>My Azure AD B2C test app</title>
    </head>
    <body>
        <h2>My Azure AD B2C test app</h2>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous" />
        <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign-in</button>
        <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign-out</button>
        <h5 id="welcome-div" class="card-header text-center d-none"></h5>
        <br />
        <!-- Content -->
        <div class="card">
            <div class="card-body text-center">
                <pre id="response" class="card-text"></pre>
                <button type="button" id="callApiButton" class="btn btn-primary d-none" onclick="passTokenToApi()">Call API</button>
            </div>
        </div>
        <script src="https://alcdn.msauth.net/browser/2.14.2/js/msal-browser.min.js" integrity="sha384-ggh+EF1aSqm+Y4yvv2n17KpurNcZTeYtUZUvhPziElsstmIEubyEB6AIVpKLuZgr" crossorigin="anonymous"></script>

        <!-- Importing app scripts (load order is important) -->
        <script type="text/javascript" src="./apiConfig.js"></script>
        <script type="text/javascript" src="./policies.js"></script>
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authRedirect.js"></script>
        <script type="text/javascript" src="./api.js"></script>
    </body>
</html>
 ```

## <a name="step-5-configure-the-authentication-library"></a>Étape 5 : configurer la bibliothèque d’authentification

Configurez le mode d’intégration de la bibliothèque MSAL.js avec Azure AD B2C. La bibliothèque MSAL.js utilise un objet de configuration courant pour se connecter aux points de terminaison d’authentification de votre locataire Azure AD B2C.

Pour configurer la bibliothèque d’authentification, effectuez ce qui suit :

1. Dans le dossier *myApp*, créez un dossier appelé *App*. 
1. Dans le dossier *app*, créez un fichier nommé *authConfig.js*.
1. Ajoutez le code JavaScript suivant au fichier *authConfig.js* :

    ```javascript
    const msalConfig = {
        auth: {
        clientId: "<Application-ID>", 
        authority: b2cPolicies.authorities.signUpSignIn.authority, 
        knownAuthorities: [b2cPolicies.authorityDomain], 
        redirectUri: "http://localhost:6420",
        },
        cache: {
        cacheLocation: "localStorage", .
        storeAuthStateInCookie: false, 
        }
    };

    const loginRequest = {
    scopes: ["openid", ...apiConfig.b2cScopes],
    };

    const tokenRequest = {
    scopes: [...apiConfig.b2cScopes],
    forceRefresh: false
    };
    ```

1. Remplacez `<Application-ID>` par l'ID d'application pour l'inscription de votre application. Pour plus d’informations, consultez [Configurer l’authentification dans un exemple d’application SPA](./configure-authentication-sample-spa-app.md#step-23-register-the-spa).

> [!TIP]
> Pour plus d’options de configuration d’objet MSAL, reportez-vous à l’article [Options d’authentification](./enable-authentication-spa-app-options.md).

## <a name="step-6-specify-your-azure-ad-b2c-user-flows"></a>Étape 6 : spécifier vos flux d’utilisateurs Azure AD B2C

Créez le fichier *policies.js*, qui fournit des informations sur votre environnement Azure AD B2C. La bibliothèque MSAL.js utilise ces informations pour créer des requêtes d’authentification à Azure AD B2C.

Pour spécifier vos flux d’utilisateur Azure AD B2C, procédez comme suit :

1. Dans le dossier *App*, créez un fichier nommé *policies.js*.
1. Ajoutez le code suivant au fichier *policies.js* :

    ```javascript
    const b2cPolicies = {
        names: {
            signUpSignIn: "B2C_1_SUSI",
            editProfile: "B2C_1_EditProfile"
        },
        authorities: {
            signUpSignIn: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-SignInOrSignUp-Policy-Id",
            },
            editProfile: {
                authority: "https://contoso.b2clogin.com/contoso.onmicrosoft.com/Your-B2C-EditProfile-Policy-Id"
            }
        },
        authorityDomain: "contoso.b2clogin.com"
    }
    ```

1. Remplacez `B2C_1_SUSI` par le nom de votre stratégie Azure AD B2C de connexion.
1. Remplacez `B2C_1_EditProfile` par le nom de votre stratégie Azure AD B2C de profil de modification.
1. Remplacez toutes les instances de `contoso` par le [nom de votre locataire Azure AD B2C](./tenant-management.md#get-your-tenant-name).

## <a name="step-7-use-the-msal-to-sign-in-the-user"></a>Étape 7 : utiliser MSAL pour la connexion de l’utilisateur

Dans cette étape, implémentez les méthodes pour initialiser le flux de connexion, l’acquisition du jeton d’accès de l’API et les méthodes de déconnexion. 

Pour plus d’informations, consultez les articles [référence de classe MSAL PublicClientApplication](https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html) et [Utiliser Microsoft Authentication Library (MSAL) pour connecter l’utilisateur](../active-directory/develop/tutorial-v2-javascript-spa.md#use-the-microsoft-authentication-library-msal-to-sign-in-the-user).

Pour connecter l’utilisateur, procédez comme suit :

1. Dans le dossier *App*, créez un fichier nommé *authRedirect.js*.
1. Dans votre *authRedirect.js*, copiez et collez le code suivant :

    ```javascript
    // Create the main myMSALObj instance
    // configuration parameters are located at authConfig.js
    const myMSALObj = new msal.PublicClientApplication(msalConfig);

    let accountId = "";
    let idTokenObject = "";
    let accessToken = null;

    myMSALObj.handleRedirectPromise()
        .then(response => {
            if (response) {
                /**
                 * For the purpose of setting an active account for UI update, we want to consider only the auth response resulting
                 * from SUSI flow. "tfp" claim in the id token tells us the policy (NOTE: legacy policies may use "acr" instead of "tfp").
                 * To learn more about B2C tokens, visit https://docs.microsoft.com/en-us/azure/active-directory-b2c/tokens-overview
                 */
                if (response.idTokenClaims['tfp'].toUpperCase() === b2cPolicies.names.signUpSignIn.toUpperCase()) {
                    handleResponse(response);
                }
            }
        })
        .catch(error => {
            console.log(error);
        });


    function setAccount(account) {
        accountId = account.homeAccountId;
        idTokenObject = account.idTokenClaims;
        myClaims= JSON.stringify(idTokenObject);
        welcomeUser(myClaims);
    }

    function selectAccount() {

        /**
         * See here for more information on account retrieval: 
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */

        const currentAccounts = myMSALObj.getAllAccounts();

        if (currentAccounts.length < 1) {
            return;
        } else if (currentAccounts.length > 1) {
        
            /**
             * Due to the way MSAL caches account objects, the auth response from initiating a user-flow
             * is cached as a new account, which results in more than one account in the cache. Here we make
             * sure we are selecting the account with homeAccountId that contains the sign-up/sign-in user-flow, 
             * as this is the default flow the user initially signed-in with.
             */
            const accounts = currentAccounts.filter(account =>
                account.homeAccountId.toUpperCase().includes(b2cPolicies.names.signUpSignIn.toUpperCase())
                &&
                account.idTokenClaims.iss.toUpperCase().includes(b2cPolicies.authorityDomain.toUpperCase())
                &&
                account.idTokenClaims.aud === msalConfig.auth.clientId 
                );

            if (accounts.length > 1) {
                // localAccountId identifies the entity for which the token asserts information.
                if (accounts.every(account => account.localAccountId === accounts[0].localAccountId)) {
                    // All accounts belong to the same user
                    setAccount(accounts[0]);
                } else {
                    // Multiple users detected. Logout all to be safe.
                    signOut();
                };
            } else if (accounts.length === 1) {
                setAccount(accounts[0]);
            }

        } else if (currentAccounts.length === 1) {
            setAccount(currentAccounts[0]);
        }
    }

    // in case of page refresh
    selectAccount();

    async function handleResponse(response) {

        if (response !== null) {
            setAccount(response.account);
        } else {
            selectAccount();
        }
    }

    function signIn() {
        myMSALObj.loginRedirect(loginRequest);
    }

    function signOut() {
        const logoutRequest = {
            postLogoutRedirectUri: msalConfig.auth.redirectUri,
        };

        myMSALObj.logoutRedirect(logoutRequest);
    }

    function getTokenRedirect(request) {
        request.account = myMSALObj.getAccountByHomeId(accountId); 

        return myMSALObj.acquireTokenSilent(request)
            .then((response) => {
                // In case the response from B2C server has an empty accessToken field
                // throw an error to initiate token acquisition
                if (!response.accessToken || response.accessToken === "") {
                    throw new msal.InteractionRequiredAuthError;
                } else {
                    console.log("access_token acquired at: " + new Date().toString());
                    accessToken = response.accessToken;
                    passTokenToApi();
                }
            }).catch(error => {
                console.log("Silent token acquisition fails. Acquiring token using popup. \n", error);
                if (error instanceof msal.InteractionRequiredAuthError) {
                    // fallback to interaction when silent call fails
                    return myMSALObj.acquireTokenRedirect(request);
                } else {
                    console.log(error);   
                }
        });
    }
    
    // Acquires and access token and then passes it to the API call
    function passTokenToApi() {
        if (!accessToken) {
            getTokenRedirect(tokenRequest);
        } else {
            try {
                callApi(apiConfig.webApi, accessToken);
            } catch(error) {
                console.log(error); 
            }
        }
    }

    function editProfile() {


        const editProfileRequest = b2cPolicies.authorities.editProfile;
        editProfileRequest.loginHint = myMSALObj.getAccountByHomeId(accountId).username;

        myMSALObj.loginRedirect(editProfileRequest);
    }
    ```

## <a name="step-8-configure-the-web-api-location-and-scope"></a>Étape 8 : configurer l’emplacement et l’étendue de l’API web

Pour permettre à votre application SPA d’appeler une API web, indiquez l’emplacement du point de terminaison de l’API web et les [étendues](./configure-authentication-sample-spa-app.md#app-registration-overview) à utiliser pour autoriser l’accès à l’API web.

Pour configurer l’emplacement et les étendues de l’API web, procédez comme suit :

1. Dans le dossier *App*, créez un fichier nommé *apiConfig.js*.
1. Dans votre *apiConfig.js*, copiez et collez le code suivant :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://contoso.onmicrosoft.com/tasks/tasks.read"],
        webApi: "https://mydomain.azurewebsites.net/tasks"
    };
    ```

1. Remplacez `contoso` par votre nom de locataire. Le nom de l’étendue requis peut se trouvé comme décrit dans l’article [Configurer les étendues](./configure-authentication-sample-spa-app.md#step-22-configure-scopes).
1. Remplacez la valeur pour `webApi` par l’emplacement de votre point de terminaison d’API web.

## <a name="step-9-call-your-web-api"></a>Étape 9 : appeler votre API web

Définissez la requête HTTP sur votre point de terminaison d’API. La requête HTTP est configurée pour transmettre le jeton d’accès acquis avec *MSAL.js* dans l’en-tête HTTP `Authorization` dans la requête.

Le code suivant définit la requête `GET` HTTP sur le point de terminaison de l’API, transmettant le jeton d’accès dans l’en-tête HTTP `Authorization`. L’emplacement de l’API est défini par la clé `webApi` dans *apiConfig.js*. 

Pour appeler votre API web à l’aide du jeton que vous avez acquis, procédez comme suit :

1. Dans le dossier *App*, créez un fichier nommé *api.js*.
1. Ajoutez le code suivant au fichier *api.js* :

    ```javascript
    function callApi(endpoint, token) {
        
        const headers = new Headers();
        const bearer = `Bearer ${token}`;
    
        headers.append("Authorization", bearer);
    
        const options = {
            method: "GET",
            headers: headers
        };
    
        logMessage('Calling web API...');
        
        fetch(endpoint, options)
        .then(response => response.json())
        .then(response => {

            if (response) {
            logMessage('Web API responded: ' + response.name);
            }
            
            return response;
        }).catch(error => {
            console.error(error);
        });
    }
    ```

## <a name="step-10-add-the-ui-elements-reference"></a>Étape 10 : ajouter la référence des éléments d’interface utilisateur

L’application SPA utilise JavaScript pour contrôler les éléments d’interface utilisateur. Par exemple, elle affiche les boutons de connexion et de déconnexion, et affiche les revendications de jeton d’ID d’utilisateur à l’écran.

Pour ajouter la référence des éléments d’interface utilisateur, procédez comme suit :

1. Dans le dossier *App*, créez un fichier nommé *ui.js*.
1. Ajoutez le code suivant au fichier *ui.js* :

    ```javascript
    // Select DOM elements to work with
    const signInButton = document.getElementById('signIn');
    const signOutButton = document.getElementById('signOut')
    const titleDiv = document.getElementById('title-div');
    const welcomeDiv = document.getElementById('welcome-div');
    const tableDiv = document.getElementById('table-div');
    const tableBody = document.getElementById('table-body-div');
    const editProfileButton = document.getElementById('editProfileButton');
    const callApiButton = document.getElementById('callApiButton');
    const response = document.getElementById("response");
    const label = document.getElementById('label');

    function welcomeUser(claims) {
        welcomeDiv.innerHTML = `Token claims: </br></br> ${claims}!`

        signInButton.classList.add('d-none');
        signOutButton.classList.remove('d-none');
        welcomeDiv.classList.remove('d-none');
        callApiButton.classList.remove('d-none');
    }

    function logMessage(s) {
        response.appendChild(document.createTextNode('\n' + s + '\n'));
    }
    ```
  
## <a name="step-11-run-your-spa-application"></a>Étape 11 : exécuter votre application SPA

Dans votre interpréteur de commandes, exécutez les commandes suivantes :

``` powershell
npm install  
npm ./index.js
```

1. Atteindre https://localhost:6420. 
1. Sélectionnez **Connexion**.
1. Terminez le processus d’inscription ou de connexion.

Une fois l’authentification réussie, le jeton d’ID analysé s’affiche à l’écran. Sélectionnez `Call API` pour appeler le point de terminaison de votre API.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[exemple de code](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa).
* Configurez les [options d’authentification dans votre propre application SPA à l’aide d’Azure AD B2C](enable-authentication-spa-app-options.md).
* [Activez l’authentification dans votre propre API web](enable-authentication-web-api.md).
