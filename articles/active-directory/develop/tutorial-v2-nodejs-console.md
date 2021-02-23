---
title: 'Tutoriel : Appeler Microsoft Graph dans une application console Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce tutoriel, vous créez une application console permettant d’appeler Microsoft Graph sur une application console Node.js.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: v-doeris
ms.openlocfilehash: 3d4211acbf6b65ef8f04d00b3936d70bb930ed9e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561064"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Tutoriel : Appeler l’API Microsoft Graph dans une application console Node.js

Dans ce tutoriel, vous créez une application console qui appelle l’API Microsoft Graph à l’aide de sa propre identité. L’application console que vous créez utilise la [bibliothèque d’authentification Microsoft (MSAL) pour Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Suivez les étapes de ce tutoriel pour :

> [!div class="checklist"]
> - Inscrire l’application dans le Portail Azure
> - Créer un projet d’application console Node.js
> - Ajouter une logique d’authentification à votre application
> - Ajouter les détails de l’inscription de l’application
> - Ajouter une méthode pour appeler une API web
> - Test de l'application

## <a name="prerequisites"></a>Prérequis

- [Node.JS](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

## <a name="register-the-application"></a>Enregistrement de l’application

Effectuez d’abord les étapes décrites dans [Inscrire une application auprès de la plateforme d’identités Microsoft](quickstart-register-app.md) pour enregistrer votre application.

Utilisez les paramètres suivants pour l’inscription de votre application :

- Nom : `NodeConsoleApp` (suggéré)
- Types de comptes pris en charge : **Comptes dans cet annuaire organisationnel uniquement**
- Autorisations de l’API : **API Microsoft** > **Microsoft Graph** > **Autorisations de l’application** > `User.Read.All`
- Secret client : `*********` (enregistrez cette valeur pour l’utiliser à une étape ultérieure ; elle n’est affichée qu’une seule fois)

## <a name="create-the-project"></a>Créer le projet

Créez un dossier pour héberger votre application, par exemple *NodeConsoleApp*.

1. Tout d’abord, accédez à votre répertoire de projet dans le terminal, puis exécutez les commandes NPM suivantes :

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Ensuite, créez un dossier nommé *bin*. À l’intérieur de ce dossier, créez un fichier nommé *index.js* et ajoutez le code suivant :

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Ce fichier fait référence à deux autres modules Node : *auth.js* qui contient une implémentation de MSAL Node pour l’acquisition de jetons d’accès, et *fetch.js* qui contient une méthode pour soumettre une requête HTTP à l’API Microsoft Graph à l’aide d’un jeton d’accès. Une fois le tutoriel terminé, la structure des fichiers et des dossiers de votre projet doit ressembler à ce qui suit :

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Ajouter une logique d’authentification

Dans le dossier *bin*, créez un autre fichier nommé *auth.js* et ajoutez le code suivant pour acquérir un jeton d’accès à présenter lors de l’appel à l’API Microsoft Graph.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation. 
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md 
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit: 
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow 
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest 
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

Dans l’extrait de code ci-dessus, nous créons d’abord un objet de configuration (*msalConfig*), puis nous le transmettons pour initialiser un objet [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) MSAL. Ensuite, nous créons une méthode pour acquérir des jetons par le biais des **informations d’identification du client** et exposons finalement ce module pour qu’il soit accessible par *main.js*. Les paramètres de configuration de ce module sont extraits d’un fichier d’environnement que nous allons créer à l’étape suivante.

## <a name="add-app-registration-details"></a>Ajouter les détails de l’inscription de l’application

Créez un fichier d’environnement pour stocker les détails de l’inscription de l’application qui seront utilisés lors de l’acquisition de jetons. Pour ce faire, créez un fichier nommé *.env* dans le dossier racine de l’exemple (*NodeConsoleApp*) et ajoutez le code suivant :

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Utilisez les valeurs que vous obtenez du portail d’inscription des applications Azure pour renseigner ces détails :

- `Enter_the_Tenant_Id_here` doit prendre l’une des valeurs suivantes :
  - Si votre application prend en charge les *comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’**ID de locataire** ou le **Nom du locataire**. Par exemple : `contoso.microsoft.com`.
  - Si votre application prend en charge les *comptes dans un annuaire organisationnel*, remplacez cette valeur par `organizations`.
  - Si votre application prend en charge les *comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par `common`.
  - Pour limiter la prise en charge aux *comptes Microsoft personnels uniquement*, remplacez cette valeur par `consumers`.
- `Enter_the_Application_Id_Here`: **ID d’application (client)** de l’application que vous avez inscrite.
- `Enter_the_Cloud_Instance_Id_Here`: instance cloud Azure dans laquelle votre application est inscrite.
  - Pour le cloud Azure principal (ou *mondial*), entrez `https://login.microsoftonline.com`.
  - Pour les clouds **nationaux** (par exemple la Chine), vous trouverez les valeurs appropriées dans [Clouds nationaux](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` est l’instance de l’API Microsoft Graph avec laquelle l’application doit communiquer.
  - Pour le point de terminaison de l’API Microsoft Graph **mondial**, remplacez les deux instances de cette chaîne par `https://graph.microsoft.com`.
  - Pour les points de terminaison dans les déploiements dans des clouds **nationaux**, consultez [Déploiements dans des clouds nationaux](/graph/deployments) dans la documentation Microsoft Graph.

## <a name="add-a-method-to-call-a-web-api"></a>Ajouter une méthode pour appeler une API web

Dans le dossier *bin*, créez un autre fichier nommé *fetch.js*, puis ajoutez le code suivant pour effectuer des appels REST à l’API Microsoft Graph :

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint 
 * @param {string} accessToken 
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Ici, la méthode `callApi` est utilisée pour soumettre une requête HTTP `GET` à une ressource protégée qui demande un jeton d’accès. La requête retourne ensuite le contenu à l’appelant. Cette méthode ajoute le jeton acquis dans l’*en-tête d’autorisation HTTP*. La ressource protégée, ici, est le [point de terminaison des utilisateurs](https://docs.microsoft.com/graph/api/user-list) de l’API Microsoft Graph qui affiche les utilisateurs du locataire dans lequel cette application est inscrite.

## <a name="test-the-app"></a>Test de l'application

Vous avez terminé la création de l’application, et êtes maintenant prêt à tester les fonctionnalités de l’application.

Démarrez l’application console Node.js en exécutant la commande suivante à la racine du dossier de votre projet :

```console
node . --op getUsers
```

Une réponse JSON provenant de l’API Microsoft Graph devrait être formulée, et vous devez voir s’afficher un tableau d’objets utilisateur dans la console :

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Interface de ligne de commande affichant la réponse Graph":::

## <a name="how-the-application-works"></a>Fonctionnement de l’application

Cette application utilise l’[Octroi des informations d’identification du client OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). Ce type d’octroi est couramment utilisé pour les interactions de serveur à serveur qui doivent s’exécuter en arrière-plan sans l’interaction immédiate d’un utilisateur. Le flux d’octroi des informations d’identification permet à un service web (client confidentiel) d’utiliser ses propres informations d’identification pour s’authentifier lorsqu’il appelle un autre service web, au lieu d’emprunter l’identité d’un utilisateur. Les types d’application pris en charge avec ce modèle d’authentification sont généralement des **démons** ou des **comptes de service**.

L'étendue à demander pour un flux d'informations d'identification client est le nom de la ressource suivi de `/.default`. Cette notation indique à Azure Active Directory (Azure AD) d’utiliser les autorisations de niveau application déclarées de manière statique lors de l’inscription de l’application. Ces autorisations d’API doivent également être accordées par un **administrateur locataire**.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez approfondir le développement d’une application console Node.js sur la plateforme d’identités Microsoft, consultez notre série de scénarios en plusieurs parties :

> [!div class="nextstepaction"]
> [Scénario : Application démon](scenario-daemon-overview.md)
