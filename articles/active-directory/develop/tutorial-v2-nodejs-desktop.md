---
title: 'Tutoriel : Connecter des utilisateurs et appeler l’API Microsoft Graph dans une application de bureau Electron | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce tutoriel, vous créez une application de bureau Electron qui peut connecter des utilisateurs et utiliser le flux du code d’authentification pour obtenir un jeton d’accès de la plateforme d’identités Microsoft et appeler l’API Microsoft Graph.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: v-doeris
ms.openlocfilehash: 82e7d01442d30356a3d8ac68262a1ac49990666c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651185"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Tutoriel : Connecter des utilisateurs et appeler l’API Microsoft Graph dans une application de bureau Electron

Dans ce tutoriel, vous créez une application de bureau Electron qui connecte les utilisateurs et appelle Microsoft Graph en utilisant le flux du code d’autorisation avec PKCE. L’application de bureau que vous créez utilise la [bibliothèque d’authentification Microsoft (MSAL) pour Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Suivez les étapes de ce tutoriel pour :

> [!div class="checklist"]
> - Inscrire l’application dans le Portail Azure
> - Créer un projet d’application de bureau Electron
> - Ajouter une logique d’authentification à votre application
> - Ajouter une méthode pour appeler une API web
> - Ajouter les détails de l’inscription de l’application
> - Test de l'application

## <a name="prerequisites"></a>Prérequis

- [Node.JS](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

## <a name="register-the-application"></a>Enregistrement de l’application

Effectuez d’abord les étapes décrites dans [Inscrire une application auprès de la plateforme d’identités Microsoft](quickstart-register-app.md) pour enregistrer votre application.

Utilisez les paramètres suivants pour l’inscription de votre application :

- Nom : `ElectronDesktopApp` (suggéré)
- Types de comptes pris en charge : **Comptes dans un annuaire d’organisation (tout annuaire Azure AD – Multilocataire) et comptes Microsoft personnel (par exemple Skype, Xbox)**
- Type de plateforme : **Applications de bureau et mobiles**
- URI de redirection : `msal://redirect`

## <a name="create-the-project"></a>Créer le projet

Créez un dossier pour héberger votre application, par exemple *ElectronDesktopApp*.

1. Tout d’abord, accédez à votre répertoire de projet dans le terminal, puis exécutez les commandes `npm` suivantes :

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Ensuite, créez un dossier nommé *App*. À l’intérieur de ce dossier, créez un fichier nommé *index.html* qui servira d’interface utilisateur. Ajoutez-y le code suivant :

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Ensuite, créez un fichier nommé *main.js* et ajoutez-y le code suivant :

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

Dans l’extrait de code ci-dessus, nous initialisons un objet de fenêtre principale Electron et créons des gestionnaires d’événements pour les interactions avec la fenêtre Electron. Nous importons également des paramètres de configuration, instancions la classe *authProvider* pour la gestion de la connexion, de la déconnexion et de l’acquisition de jetons, et appelons l’API Microsoft Graph.

4. Dans le même dossier (*App*), créez un autre fichier nommé *renderer.js* et ajoutez le code suivant :

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Pour finir, créez un fichier nommé *constants.js*, qui stockera les constantes de chaînes permettant de décrire les **événements** d’application :

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Vous disposez à présent d’une interface graphique utilisateur simple, et d’interactions pour votre application Electron. Une fois le tutoriel terminé, la structure des fichiers et des dossiers de votre projet doit ressembler à ce qui suit :

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Ajouter une logique d’authentification à votre application

Dans le dossier *App*, créez un fichier nommé *AuthProvider.js*. Il contiendra une classe de fournisseur d’authentification, prévue pour gérer la connexion, la déconnexion, l’acquisition de jetons, la sélection de compte et les tâches d’authentification associées, à l’aide de MSAL Node. Ajoutez-y le code suivant :

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of 
 * a custom file protocol instead of a regular web (https://) redirect URI in order to 
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation. 
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md 
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md 
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;
        
        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);
        
        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         * 
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         * 
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         * 
         * For details on PKCE code generation logic, consult the 
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = { 
            ...this.authCodeUrlParams, 
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method 
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);
        
        const authResponse = await this.clientApplication.acquireTokenByCode({ 
            ...this.authCodeRequest, 
            scopes: tokenRequest.scopes, 
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier 
        });
        
        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {
        
        authWindow.loadURL(navigateUrl);

        return new Promise((resolve, reject) => {
            authWindow.webContents.on('will-redirect', (event, responseUrl) => {
                try {
                    const parsedUrl = new URL(responseUrl);
                    const authCode = parsedUrl.searchParams.get('code');
                    resolve(authCode);
                } catch (err) {
                    reject(err);
                }
            });
        });
    }

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response 
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

Dans l’extrait de code ci-dessus, nous avons d’abord initialisé `PublicClientApplication` MSAL Node en passant un objet de configuration (`msalConfig`). Nous avons ensuite exposé les méthodes `login`, `logout` et `getToken` pour qu’elles soient appelées par le module principal (*main.js*). Dans `login` et `getToken`, nous obtenons des jetons d’ID et d’accès, respectivement, en demandant d’abord un code d’autorisation, puis en l’échangeant pour un jeton à l’aide d’une API publique `acquireTokenByCode` MSAL Node.

## <a name="add-a-method-to-call-a-web-api"></a>Ajouter une méthode pour appeler une API web

Créez un autre fichier nommé *fetch.js*. Ce fichier contiendra un client HTTP Axios permettant passer des appels REST à l’API Microsoft Graph.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint 
 * @param accessToken 
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Ajouter les détails de l’inscription de l’application

Pour terminer, créez un fichier d’environnement pour stocker les détails de l’inscription de l’application qui seront utilisés lors de l’acquisition de jetons. Pour ce faire, créez un fichier nommé *.env* dans le dossier racine de l’exemple (*ElectronDesktopApp*) et ajoutez le code suivant :

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Utilisez les valeurs que vous obtenez du portail d’inscription des applications Azure pour renseigner ces détails :

- `Enter_the_Tenant_Id_here` doit prendre l’une des valeurs suivantes :
  - Si votre application prend en charge les *comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’**ID de locataire** ou le **Nom du locataire**. Par exemple : `contoso.microsoft.com`.
  - Si votre application prend en charge les *comptes dans un annuaire organisationnel*, remplacez cette valeur par `organizations`.
  - Si votre application prend en charge les *comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par `common`.
  - Pour limiter la prise en charge aux *comptes Microsoft personnels uniquement*, remplacez cette valeur par `consumers`.
- `Enter_the_Application_Id_Here`: **ID d’application (client)** de l’application que vous avez inscrite.
- `Enter_the_Cloud_Instance_Id_Here`: instance cloud Azure dans laquelle votre application est inscrite.
  - Pour le cloud Azure principal (ou *mondial*), entrez `https://login.microsoftonline.com/`.
  - Pour les clouds **nationaux** (par exemple la Chine), vous trouverez les valeurs appropriées dans [Clouds nationaux](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` est l’instance de l’API Microsoft Graph avec laquelle l’application doit communiquer.
  - Pour le point de terminaison de l’API Microsoft Graph **mondial**, remplacez les deux instances de cette chaîne par `https://graph.microsoft.com/`.
  - Pour les points de terminaison dans les déploiements dans des clouds **nationaux**, consultez [Déploiements dans des clouds nationaux](/graph/deployments) dans la documentation Microsoft Graph.

## <a name="test-the-app"></a>Test de l'application

Vous venez de terminer la création de l’application et êtes maintenant prêt à lancer l’application de bureau Electron pour tester les fonctionnalités de l’application.

1. Démarrez l’application en exécutant la commande suivante à la racine du dossier de votre projet :

```console
electron App/main.js
```

2. Dans la fenêtre principale de l’application, vous devez voir apparaître le contenu de votre fichier *index.html*, ainsi que le bouton **Se connecter**.

## <a name="test-sign-in-and-sign-out"></a>Tester la connexion et la déconnexion

Une fois le fichier *index.html* chargé, sélectionnez **Se connecter**. Vous êtes invité à vous connecter avec la plateforme d’identité Microsoft :

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="Invite de connexion":::

Si vous consentez à accorder les autorisations demandées, l’application web affiche votre nom d’utilisateur, ce qui signifie que la connexion a réussi :

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="Connexion réussie":::

## <a name="test-web-api-call"></a>Tester l’appel à l’API web

Une fois connecté, sélectionnez **See Profile** pour afficher les informations de profil utilisateur retournées dans la réponse à l’appel à l’API Microsoft Graph :

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="Informations de profil à partir de Microsoft Graph":::

Sélectionnez **Read Mails** (Lire les e-mails) pour afficher les messages dans le compte de l’utilisateur. Un écran de consentement s’affiche :

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="écran de consentement pour l’autorisation read.mail":::

Après le consentement, vous voyez s’afficher les messages retournés dans la réponse de l’appel à l’API Microsoft Graph :

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="Informations de messagerie depuis Microsoft Graph":::

## <a name="how-the-application-works"></a>Fonctionnement de l’application

Quand un utilisateur sélectionne le bouton **Se connecter** pour la première fois, la méthode get `getTokenInteractive` de *AuthProvider.js* est appelée. Cette méthode redirige l’utilisateur vers une connexion via le *point de terminaison de la plateforme d’identités Microsoft* avec validation des informations d’identification de l’utilisateur, et obtient alors un **code d’autorisation**. Ce code est ensuite échangé pour un jeton d’accès à l’aide de l’API publique `acquireTokenByCode` de MSAL Node.

Un code d’autorisation protégé par PKCE est alors envoyé au point de terminaison de jeton protégé par CORS en vue de l’échange de jetons. Votre application reçoit un jeton d’ID, un jeton d’accès et un jeton d’actualisation qui sont traités par MSAL Node ; les informations contenues dans les jetons sont mises en cache.

Le jeton d’ID contient des informations générales sur l’utilisateur, comme son nom d’affichage. Le jeton d’accès a une durée de vie limitée et expire au bout de 24 heures. Si vous envisagez d’utiliser ces jetons pour accéder à une ressource protégée, votre serveur back-end *doit* les valider afin de garantir leur émission pour un utilisateur autorisé de votre application.

L’application de bureau que vous avez créée dans ce tutoriel passe un appel REST à l’API Microsoft Graph au moyen d’un jeton d’accès utilisé comme jeton du porteur dans l’en-tête de requête ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

L’API Microsoft Graph nécessite l’étendue *user.read* pour lire le profil d’un utilisateur. Par défaut, cette étendue est automatiquement ajoutée à toutes les applications inscrites dans le Portail Azure. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, l’API Microsoft Graph nécessite l’étendue *Mail.Read* afin de lister l’e-mail de l’utilisateur.

À mesure que vous ajoutez des étendues, vos utilisateurs peuvent être invités à accorder un consentement supplémentaire pour les étendues ajoutées.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez approfondir le développement d’une application de bureau Electron Node.js sur la plateforme d’identités Microsoft, consultez notre série de scénarios en plusieurs parties :

> [!div class="nextstepaction"]
> [Scénario : Application de bureau qui appelle des API web](scenario-desktop-overview.md)
