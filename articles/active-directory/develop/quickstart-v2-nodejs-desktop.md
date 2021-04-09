---
title: 'Démarrage rapide : Appeler Microsoft Graph depuis une application de bureau Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez découvrir comment une application de bureau Electron Node.js peut connecter des utilisateurs et obtenir un jeton d’accès pour appeler une API protégée par un point de terminaison de plateforme d’identités Microsoft.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653267"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Démarrage rapide : Acquérir un jeton d’accès et appeler l’API Microsoft Graph depuis une application de bureau Electron

Dans ce guide de démarrage rapide, vous téléchargez et vous exécutez un exemple de code qui montre comment une application de bureau Electron peut connecter des utilisateurs et acquérir des jetons d’accès pour appeler l’API Microsoft Graph.

Ce guide de démarrage rapide utilise la [bibliothèque d’authentification Microsoft pour Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) avec le [flux de code d’autorisation avec PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Inscrire et télécharger l’exemple d’application
>
> Suivez les étapes ci-dessous pour commencer.
>
> #### <a name="step-1-register-the-application"></a>Étape 1 : Enregistrement de l’application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoire + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour sélectionner le locataire dans lequel vous voulez inscrire une application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Entrez un **nom** pour votre application (par exemple, `msal-node-desktop`). Les utilisateurs de votre application peuvent voir ce nom, et vous pouvez le changer ultérieurement.
> 1. Sélectionnez **Inscrire** pour créer l’application.
> 1. Sous **Gérer**, sélectionnez **Authentification**.
> 1. Sélectionnez **Ajouter une plateforme** > **Applications de bureau et mobiles**.
> 1. Dans la section **URI de redirection**, entrez `msal://redirect`.
> 1. Sélectionnez **Configurer**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Étape 1 : Configurer l’application dans le portail Azure
> Pour que l’exemple de code de ce guide de démarrage rapide fonctionne, vous devez ajouter une URL de réponse comme **msal://redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter cette modification pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-windows-desktop/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-the-electron-sample-project"></a>Étape 2 : Télécharger l’exemple de projet Electron

> [!div renderon="docs"]
> [Téléchargez l’exemple de code](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip).

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Étape 3 : Configurer l’exemple de projet Electron
>
> 1. Extrayez le fichier zip dans un dossier local proche de la racine du disque, par exemple *C:\Azure-Samples*.
> 1. Éditez *.env* et remplacez les valeurs des champs `TENANT_ID`, `CLIENT_ID` par l’extrait de code suivant :
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Où :
>    - `Enter_the_Application_Id_Here` : est l’**ID d’application (client)** pour l’application que vous avez inscrite.
>    - `Enter_the_Tenant_Id_Here` : remplacez cette valeur par l’**ID du locataire** ou le **nom du locataire** (par exemple, contoso.microsoft.com)
>
> > [!TIP]
> > Pour connaître les valeurs de l’**ID d’application (client)** et de l’**ID de l’annuaire (locataire)** , consultez la page **Vue d’ensemble** de l’application dans le Portail Azure.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application

Vous devez installer les dépendances de cet exemple une seule fois :

```console
npm install
```

Ensuite, exécutez l’application via l’invite de commandes ou la console :

```console
npm start
```

Vous devez voir l’interface utilisateur de l’application avec un bouton **Se connecter**.

## <a name="about-the-code"></a>À propos du code

Voici quelques-uns des aspects importants de l’exemple d’application.

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Pour plus d’informations sur l’utilisation de MSAL Node avec des applications de bureau, consultez [cet article](scenario-desktop-overview.md).

Vous pouvez installer MSAL Node en exécutant la commande npm suivante.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence pour MSAL Node en ajoutant le code suivant :

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Où : |Description |
> |---------|---------|
> | `clientId` | Est l’**ID d’application (client)** de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
> | `authority`    | Point de terminaison STS pour l’utilisateur à authentifier. Généralement `https://login.microsoftonline.com/{tenant}` pour un cloud public, où {tenant} est le nom ou l’ID de votre locataire.|

### <a name="requesting-tokens"></a>Demande de jetons

Dans le premier tronçon du flux de code d’autorisation avec PKCE, préparez et envoyez une demande de code d’autorisation avec les paramètres appropriés. Ensuite, dans le deuxième tronçon du flux, écoutez la réponse du code d’autorisation. Une fois le code obtenu, échangez-le pour obtenir un jeton.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

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
```

> |Où :| Description |
> |---------|---------|
> | `authWindow` | Fenêtre Electron actuelle en traitement. |
> | `tokenRequest` | Contient les étendues demandées, comme `"User.Read"` pour Microsoft Graph ou `"api://<Application ID>/access_as_user"` pour les API web personnalisées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement d’applications de bureau avec MSAL Node, consultez le tutoriel :

> [!div class="nextstepaction"]
> [Tutoriel : Connecter des utilisateurs et appeler l’API Microsoft Graph dans une application de bureau Electron](tutorial-v2-nodejs-desktop.md)