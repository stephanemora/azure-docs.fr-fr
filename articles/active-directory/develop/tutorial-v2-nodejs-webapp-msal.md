---
title: 'Tutoriel : Connecter des utilisateurs dans une application web Node.js & Express | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce tutoriel, vous ajoutez la prise en charge de la connexion des utilisateurs dans une application web.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648988"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Tutoriel : Connecter des utilisateurs dans une application web Node.js & Express

Dans ce tutoriel, vous créez une application web qui connecte les utilisateurs. L’application web que vous créez utilise la [bibliothèque d’authentification Microsoft (MSAL) pour Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Suivez les étapes de ce tutoriel pour :

> [!div class="checklist"]
> - Inscrire l’application dans le Portail Azure
> - Créer un projet d’application web Express
> - Installer les packages de la bibliothèque d’authentification
> - Ajouter les détails de l’inscription de l’application
> - Ajouter le code pour la connexion des utilisateurs
> - Test de l'application

## <a name="prerequisites"></a>Prérequis

- [Node.JS](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

## <a name="register-the-application"></a>Enregistrement de l’application

Effectuez d’abord les étapes décrites dans [Inscrire une application auprès de la plateforme d’identités Microsoft](quickstart-register-app.md) pour enregistrer votre application.

Utilisez les paramètres suivants pour l’inscription de votre application :

- Nom : `ExpressWebApp` (suggéré)
- Types de comptes pris en charge : **Comptes dans un annuaire d’organisation (tout annuaire Azure AD – Multilocataire) et comptes Microsoft personnels (par exemple Skype, Xbox)**
- Type de plateforme : **Web**
- URI de redirection : `http://localhost:3000/redirect`
- Secret client : `*********` (enregistrez cette valeur pour l’utiliser à une étape ultérieure ; elle n’est affichée qu’une seule fois)

## <a name="create-the-project"></a>Créer le projet

Créez un dossier pour héberger votre application, par exemple *ExpressWebApp*.

1. Tout d’abord, accédez à votre répertoire de projet dans le terminal, puis exécutez les commandes `npm` suivantes :

```console
    npm init -y
    npm install --save express
```

2. Ensuite, créez un fichier nommé *index.js* et ajoutez le code suivant :

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Vous disposez à présent d’un serveur web simple qui s’exécute sur le port 3000. La structure des fichiers et des dossiers de votre projet doit ressembler à ce qui suit :

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Installer la bibliothèque d’authentification

Localisez la racine du répertoire de votre projet dans un terminal et installez le package MSAL Node via NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Ajouter les détails de l’inscription de l’application

Dans le fichier *index.js* que vous avez créé précédemment, ajoutez le code suivant :

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
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
- `Enter_the_Client_secret` : Remplacez cette valeur par le secret client que vous avez créé précédemment. Pour générer une nouvelle clé, utilisez **Certificats et secrets** dans les paramètres de l’inscription de l’application sur le portail Azure.

> [!WARNING]
> Tout secret en texte clair dans le code source présente un risque accru pour la sécurité. Cet article utilise un secret client en texte clair uniquement pour rester simple. Utilisez des [informations d’identification de certificat](active-directory-certificate-credentials.md) plutôt que des secrets client dans vos applications clientes confidentielles, en particulier dans celles que vous prévoyez de déployer en production.

## <a name="add-code-for-user-login"></a>Ajouter le code pour la connexion des utilisateurs

Dans le fichier *index.js* que vous avez créé précédemment, ajoutez le code suivant :

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Tester la connexion

Vous avez terminé la création de l’application, et êtes maintenant prêt à tester les fonctionnalités de l’application.

1. Démarrez l’application console Node.js en exécutant la commande suivante à la racine du dossier de votre projet :

```console
   node index.js
```

2. Ouvrez une fenêtre de navigateur et accédez à `http://localhost:3000`. Vous devriez voir un écran de connexion s’afficher :

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Affichage de l’écran de connexion Azure AD":::

3. Une fois vos informations d’identification entrées, un écran de consentement devrait s’afficher pour vous demander d’approuver les autorisations se rapportant à l’application.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Affichage de l’écran de consentement Azure AD":::

## <a name="how-the-application-works"></a>Fonctionnement de l’application

Dans ce tutoriel, vous avez initialisé un objet [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) MSAL Node en lui transmettant un objet de configuration (*msalConfig*) qui contient les paramètres obtenus de l’inscription de votre application Azure AD sur le portail Azure. L’application web que vous avez créée utilise le [flux d’octroi du code d’autorisation OAuth 2.0](./v2-oauth2-auth-code-flow.md) pour connecter des utilisateurs et obtenir des jetons d’ID et d’accès.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez approfondir le développement d’applications web Node.js & Express sur la plateforme d’identités Microsoft, consultez notre série de scénarios en plusieurs parties :

> [!div class="nextstepaction"]
> [Scénario : application web qui connecte les utilisateurs](scenario-web-app-sign-user-overview.md)