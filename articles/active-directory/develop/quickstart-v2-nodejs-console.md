---
title: 'Démarrage rapide : Appeler Microsoft Graph dans une application console Node.js | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce guide de démarrage rapide, vous allez découvrir comment une application console Node.js peut obtenir un jeton d’accès et appeler une API protégée par un point de terminaison de plateforme d’identités Microsoft, en utilisant l’identité propre de l’application
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/11/2021
ms.author: v-doeris
ms.openlocfilehash: c550cc8009f0138b9f1803399fbc592b34efbfab
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562014"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Démarrage rapide : Acquérir un jeton et appeler l’API Microsoft Graph à partir d’une application console Node.js en utilisant l’identité de l’application

Dans ce guide de démarrage rapide, vous téléchargez et vous exécutez un exemple de code qui montre comment une application Node.js peut obtenir un jeton d’accès en utilisant l’identité de l’application pour appeler l’API Microsoft Graph et afficher une [liste d’utilisateurs](/graph/api/user-list) dans l’annuaire. L’exemple de code montre comment un travail sans assistance ou un service Windows peut s’exécuter avec l’identité d’une application, au lieu de l’identité d’un utilisateur.

Ce guide de démarrage rapide utilise la [bibliothèque d’authentification Microsoft pour Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) avec l’[octroi des informations d’identification du client](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Prérequis

* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur de code

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Inscrire et télécharger votre application de démarrage rapide
>
> Suivez les étapes ci-dessous pour commencer.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Étape 1 : Inscrivez votre application
> Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :
>
> 1. Connectez-vous au <a href="https://portal.azure.com/" target="_blank">portail Azure</a>.
> 1. Si vous avez accès à plusieurs locataires, utilisez le filtre **Répertoire + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: dans le menu du haut pour sélectionner le locataire dans lequel vous voulez inscrire une application.
> 1. Recherchez et sélectionnez **Azure Active Directory**.
> 1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.
> 1. Entrez un **nom** pour votre application (par exemple, `msal-node-cli`). Les utilisateurs de votre application peuvent voir ce nom, et vous pouvez le changer ultérieurement.
> 1. Sélectionnez **Inscription**.
> 1. Sous **Gérer**, sélectionnez **Certificats et secrets**.
> 1. Sous **Secrets du client**, sélectionnez **Nouveau secret client**, entrez un nom, puis sélectionnez **Ajouter**. Enregistrez la valeur secrète dans un emplacement sûr pour l’utiliser dans une étape ultérieure.
> 1. Sous **Gérer**, sélectionnez **Autorisations de l’API** > **Ajouter une autorisation**. Sélectionnez **Microsoft Graph**.
> 1. Sélectionnez **Autorisations de l’application**.
> 1. Sous le nœud **Utilisateur**, sélectionnez **User.Read.All**, puis sélectionnez **Ajouter des autorisations**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Télécharger et configurer votre application de démarrage rapide
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Étape 1 : Configurer votre application dans le portail Azure
> Pour que l’exemple de code fonctionne dans ce guide de démarrage rapide, vous devez créer un secret client et ajouter l’autorisation d’application **User.Read.All** de l’API Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporter ces modifications pour moi]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Déjà configuré](media/quickstart-v2-netcore-daemon/green-check.png) Votre application est configurée avec ces attributs.

#### <a name="step-2-download-your-nodejs-project"></a>Étape 2 : Télécharger votre projet Node.js

> [!div renderon="docs"]
> [Téléchargez l’exemple de code](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip).

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Téléchargez l’exemple de code](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-nodejs-project"></a>Étape 3 : Configurer votre projet Node.js
>
> 1. Extrayez le fichier zip dans un dossier local proche de la racine du disque, par exemple *C:\Azure-Samples*.
> 1. Éditez *.env* et remplacez les valeurs des champs `TENANT_ID`, `CLIENT_ID` et `CLIENT_SECRET` par l’extrait de code suivant :
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Où :
>    - `Enter_the_Application_Id_Here` - est l’**ID (de client) d’application** de l’application que vous avez inscrite précédemment. Recherchez cet ID dans le volet **Vue d’ensemble** de l’inscription de l’application dans le portail Azure.
>    - `Enter_the_Tenant_Id_Here` - remplacez cette valeur par l’**ID de locataire** ou le **Nom du locataire** (par exemple contoso.microsoft.com).  Recherchez ces valeurs dans le volet **Vue d’ensemble** de l’inscription de l’application dans le portail Azure.
>    - `Enter_the_Client_Secret_Here` - remplacez cette valeur par le secret client que vous avez créé précédemment. Pour générer une nouvelle clé, utilisez **Certificats et secrets** dans les paramètres d’inscription de l’application dans le portail Azure.
>
> > [!WARNING]
> > Tout secret en texte clair dans le code source présente un risque accru pour la sécurité. Cet article utilise un secret client en texte clair uniquement pour des raisons de simplicité. Utilisez les [informations d’identification de certificat](active-directory-certificate-credentials.md) au lieu des secrets client dans vos applications clientes confidentielles, en particulier celles que vous prévoyez de déployer en production.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Étape 3 : Consentement de l’administrateur

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Étape 4 : Consentement de l’administrateur

Si vous essayez d’exécuter l’application à ce stade, vous recevez l’erreur *HTTP 403 - Interdit* : `Insufficient privileges to complete the operation`. Cette erreur se produit parce que les *autorisations d’application uniquement* nécessitent le **consentement de l’administrateur** : un administrateur général de votre annuaire doit donner son consentement à votre application. Sélectionnez l’une des options ci-dessous en fonction de votre rôle :

##### <a name="global-tenant-administrator"></a>Administrateur de locataires général

> [!div renderon="docs"]
> Si vous avez le rôle d’administrateur de locataires général, accédez à la page **Autorisations de l’API** dans Inscription d’applications sur le portail Azure, puis sélectionnez **Accorder le consentement de l’administrateur pour {nom du locataire}** (où {nom du locataire} est le nom de votre annuaire).

> [!div renderon="portal" class="sxs-lookup"]
> Si vous êtes administrateur général, accédez à la page **Autorisations de l’API** et sélectionnez **Accorder le consentement de l’administrateur pour Entrer_le_nom_du_locataire_ici**
> > [!div id="apipermissionspage"]
> > [Accéder à la page Autorisations de l’API]()

##### <a name="standard-user"></a>Utilisateur standard

Si vous êtes utilisateur standard de votre locataire, vous devez demander à un administrateur général de vous accorder son **consentement d’administrateur** pour votre application. Pour ce faire, donnez l’URL suivante à votre administrateur :

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Où :
>> * `Enter_the_Tenant_Id_Here` : remplacez cette valeur par l’**ID du locataire** ou le **nom du locataire** (par exemple, contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` : est l’**ID d’application (client)** pour l’application que vous avez inscrite.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Étape 4 : Exécution de l'application

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Étape 5 : Exécution de l'application

Localisez le dossier racine de l’exemple (c’est là où se trouve `package.json`) dans une invite de commandes ou une console. Vous devez installer les dépendances de cet exemple une seule fois :

```console
npm install
```

Ensuite, exécutez l’application via l’invite de commandes ou la console :

```console
node . --op getUsers
```

Vous voyez normalement sur la sortie de la console un fragment JSON représentant une liste d’utilisateurs dans votre annuaire Azure AD.

## <a name="about-the-code"></a>À propos du code

Voici quelques-uns des aspects importants de l’exemple d’application.

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) est la bibliothèque utilisée pour connecter les utilisateurs et demander des jetons permettant d’accéder à une API protégée par la plateforme d’identités Microsoft. Comme vous l’avez vu, ce guide de démarrage rapide demande des jetons par des autorisations d’application (en utilisant l’identité propre de l’application) au lieu d’autorisations déléguées. Le flux d’authentification utilisé dans ce cas est désigné sous le nom de [flux d’informations d’identification de client OAuth 2.0](v2-oauth2-client-creds-grant-flow.md). Pour plus d’informations sur l’utilisation de MSAL Node avec des applications démon, consultez [Scénario : Application démon](scenario-daemon-overview.md).

 Vous pouvez installer MSAL Node en exécutant la commande npm suivante.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Initialisation MSAL

Vous pouvez ajouter la référence de MSAL en ajoutant le code suivant :

```javascript
const msal = require('@azure/msal-node');
```

Ensuite, initialisez MSAL à l’aide du code suivant :

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   } 
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Où : |Description |
> |---------|---------|
> | `clientId` | Est l’**ID d’application (client)** de l’application inscrite dans le portail Azure. Vous pouvez retrouver cette valeur dans la page **Vue d’ensemble** de l’application dans le portail Azure. |
> | `authority`    | Point de terminaison STS pour l’utilisateur à authentifier. Généralement `https://login.microsoftonline.com/{tenant}` pour un cloud public, où {tenant} est le nom ou l’ID de votre locataire.|
> | `clientSecret` | Est le secret client créé pour l’application dans le portail Azure. |

Pour plus d’informations, consultez la [documentation de référence sur `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Demande de jetons

Pour demander un jeton à l’aide de l’identité d’application, utilisez la méthode `acquireTokenByClientCredential` :

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Où :| Description |
> |---------|---------|
> | `tokenRequest` | Contient les étendues demandées. Pour les clients confidentiels, utilisez un format similaire à `{Application ID URI}/.default`. Ce format indique que les étendues demandées sont celles qui sont définies de manière statique dans l’objet application défini dans le portail Azure (pour Microsoft Graph, `{Application ID URI}` pointe vers `https://graph.microsoft.com`). Pour les API web personnalisées, `{Application ID URI}` est défini sous la section **Exposer une API** dans Inscription d’application sur le portail Azure. |
> | `tokenResponse` | La réponse contient un jeton d’accès pour les étendues demandées. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement d’applications démon/console avec MSAL Node, consultez le tutoriel :

> [!div class="nextstepaction"]
> [Application démon qui appelle des API web](tutorial-v2-nodejs-console.md)