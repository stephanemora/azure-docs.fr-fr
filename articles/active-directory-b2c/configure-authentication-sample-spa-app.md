---
title: Configurer l’authentification dans un exemple d’application monopage à l’aide d’Azure Active Directory B2C
description: Utilisation d’Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application monopage.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/11/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: addf3870c22105a2ff42202e768d1e8cda4ffbde
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112073004"
---
# <a name="configure-authentication-in-a-sample-single-page-application-using-azure-active-directory-b2c"></a>Configurer l’authentification dans un exemple d’application monopage à l’aide d’Azure Active Directory B2C

Cet article utilise un exemple d’application monopage JavaScript pour illustrer comment ajouter une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications monopages.

## <a name="overview"></a>Vue d’ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0 que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple d’application monopage utilise [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) et le flux PKCE d’OIDC. MSAL.js est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications monopages.

### <a name="sign-in-flow"></a>Flux de connexion
Le flux de connexion implique les étapes suivantes :

1. L’utilisateur accède à l’application web et sélectionne **Connexion**. 
1. L’application initie une demande d’authentification et redirige l’utilisateur vers Azure AD B2C.
1. L’utilisateur [s’inscrit ou se connecte](add-sign-up-and-sign-in-policy.md), [réinitialise le mot de passe](add-password-reset-policy.md) ou se connecte avec un [compte social](add-identity-provider.md).
1. Une fois la connexion établie, Azure AD B2C renvoie un jeton d’ID à l’application.
1. L’application monopage valide le jeton d’ID, lit les revendications et permet à l’utilisateur d’appeler des ressources ou des API protégées.

### <a name="app-registration-overview"></a>Vue d’ensemble de l’inscription de l’application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, vous devez inscrire deux applications dans le répertoire d’Azure AD B2C.  

- L’inscription de l’**application web** permet à votre application de se connecter grâce à Azure AD B2C. Pendant l’inscription de l’application, vous spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel l’utilisateur est redirigé après s’être authentifié avec Azure AD B2C. Le processus d’inscription de l’application génère un *ID d’application*, également appelé *ID client*, qui identifie votre application de façon unique.

- L’inscription de l’**API web** permet à votre application d’appeler une API web sécurisée. L’inscription comprend les *étendues* de l’API web. Les étendues permettent de gérer les autorisations d’accès aux ressources protégées, telles que votre API web. Vous accordez des permissions d’application web aux étendues de l’API web. Lorsqu’un jeton d’accès est demandé, votre application spécifie les permissions souhaitées dans le paramètre d’étendue de la requête.  

Les diagrammes suivants décrivent les inscriptions et l’architecture des applications.

![Application web avec inscriptions et jetons d’appel d’API web](./media/configure-authentication-sample-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>Flux de déconnexion

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Un ordinateur qui exécute :

* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur de code
* [Runtime Node.js](https://nodejs.org/en/download/)

## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-spa-and-api"></a>Étape 2 : Inscrire votre application monopage et votre API

Au cours de cette étape, vous allez créer les inscriptions d’application de l’application monopage et de l’API web, puis spécifier les étendues de votre API web.

### <a name="21-register-the-web-api-application"></a>2.1 Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 Configurer des étendues

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-client-app"></a>2.3 Inscrire l’application cliente

Effectuez les étapes suivantes pour créer l’inscription d’application :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez l’icône **Annuaire et abonnement** dans la barre d’outils du portail, puis sélectionnez l’annuaire qui contient votre locataire Azure AD B2C.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Entrez un **Nom** pour l’application. Par exemple, *MyApp*.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Application monopage (SPA)** , puis entrez `http://localhost:6420` dans la zone de texte de l’URL.
1. Sous **Autorisations**, cochez la case **Accorder le consentement administrateur aux autorisations openid et offline access**.
1. Sélectionnez **Inscription**.

Ensuite, activez le flux d’octroi implicite :

1. Sous Gérer, sélectionnez Authentification.
1. Sélectionnez Essayer la nouvelle expérience (si elle est indiquée).
1. Sous Octroi implicite, cochez la case Jetons d’ID.
1. Sélectionnez Enregistrer.

Enregistrez **l’ID d’application (client)** que vous utiliserez ultérieurement pour configurer l'application web.
    ![Obtenir votre ID d’application](./media/configure-authentication-sample-web-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 Accorder des autorisations

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-spa-sample-code"></a>Étape 3 : Obtenir le code de l’exemple d’application monopage

Cet exemple montre la façon dont une application monopage peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs et appeler une API web protégée. Téléchargez l’exemple ci-dessous :

  [Téléchargez un fichier zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) ou clonez l’exemple à partir de GitHub :

  ```
  git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
  ```

### <a name="31-update-the-spa-sample"></a>3.1 Mettre à jour l’exemple d’application monopage

Maintenant que vous avez obtenu l’exemple, mettez à jour le code à l’aide du nom de votre locataire Azure AD B2C et de l’ID d’application *myApp* que vous avez enregistré à l’étape 2.3.

Ouvrez le fichier *authConfig.js* dans le dossier *App*.
1. Dans l’objet `msalConfig`, recherchez l’affectation pour `clientId` et remplacez-la par l’**ID d’application (client)** que vous avez enregistré à l’étape 2.3.

Ouvrez le fichier `policies.js`.
1. Recherchez les entrées sous `names` et remplacez leur affectation par le nom des flux utilisateur que vous avez créés à une étape antérieure, par exemple `b2c_1_susi`.
1. Recherchez les entrées sous `authorities` et remplacez-les par les noms des flux utilisateur que vous avez créés à une étape antérieure, par exemple `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`.
1. Recherchez l’affectation pour `authorityDomain` et remplacez-la par `<your-tenant-name>.b2clogin.com`.

Ouvrez le fichier `apiConfig.js`.
1. Recherchez l’affectation pour `b2cScopes` et remplacez l’URL par l’URL d’étendue que vous avez créée pour l’API web, par exemple `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`.
1. Recherchez l’affectation pour `webApi` et remplacez l’URL actuelle par `http://localhost:5000/tasks`.


Le code obtenu doit ressembler à l’exemple suivant :

*authConfig.js* :

```javascript
const msalConfig = {
  auth: {
    clientId: "<your-MyApp-application-ID>"
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes
};
```

*policies.js* :

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "your-tenant-name.b2clogin.com"
}
```

*apiConfig.js* :

```javascript
const apiConfig = {
  b2cScopes: ["https://your-tenant-name.onmicrosoft.com/tasks-api/tasks.read"],
  webApi: "http://localhost:5000/tasks"
};
```

## <a name="step-4-get-the-web-api-sample-code"></a>Étape 4 : Obtenir le code de l’exemple d’API web

Maintenant que l’API web est inscrite et que vous avez défini ses étendues, configurez le code de l’API web pour qu’il fonctionne avec votre locataire Azure AD B2C. Téléchargez l’exemple ci-dessous :

[Téléchargez une \*archive .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clonez l’exemple de projet d’API web à partir de GitHub. Vous pouvez également accéder directement au projet [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) sur GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-update-the-web-api"></a>4.1 Mettre à jour l’API web

1. Ouvrez le fichier *config.json* dans votre éditeur de code.
1. Modifiez les valeurs des variables par celles de l’inscription d’application que vous avez créée précédemment. Mettez également à jour le `policyName` avec le workflow utilisateur que vous avez créé dans le cadre des prérequis. Par exemple, *b2c_1_susi*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    ```

### <a name="42-enable-cors"></a>4.2 Activer CORS

Pour autoriser votre application monopage à appeler l’API web Node.js, vous devez activer [CORS](https://expressjs.com/en/resources/middleware/cors.html) dans l’API web. Dans une application de production, vous devez être attentif au domaine qui effectue la demande. Dans cet exemple, autorisez les demandes provenant de n’importe quel domaine.

Pour activer CORS, utilisez l’intergiciel (middleware) suivant. Dans le code de l’exemple d’API web Node.js que vous avez téléchargé, il a déjà été ajouté au fichier *index.js*.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

## <a name="step-5-run-the-spa-and-web-api"></a>Étape 5 : Exécuter l’application monopage et l’API web

Vous êtes maintenant prêt à tester l’accès délimité de l’application monopage à l’API. Enfin, vous exécutez à la fois l’API web Node.js et l’exemple d’application monopage JavaScript sur votre machine locale. Ensuite, vous vous connectez à l’application monopage et vous sélectionnez le bouton **Call API** (Appeler l’API) pour envoyer une demande à l’API protégée.

### <a name="run-the-nodejs-web-api"></a>Exécuter l’API web Node.js

1. Ouvrez une fenêtre de console et accédez au répertoire contenant l’exemple d’API web Node.js. Par exemple :

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    node index.js
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Exécuter l’application monopage

1. Ouvrez une autre fenêtre de console et accédez au répertoire contenant l’exemple d’application monopage JavaScript. Par exemple :

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    npm start
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application.

    ```console
    Listening on port 6420...
    ```

1. Accédez à `http://localhost:6420` dans votre navigateur pour voir l’application.

    ![Exemple d’application monopage affichée dans un navigateur](./media/configure-authentication-sample-spa-app/sample-app-sign-in.png)

1. Connectez-vous en utilisant l’adresse e-mail et le mot de passe que vous avez utilisés dans le [tutoriel précédent](tutorial-single-page-app.md). Une fois la connexion établie, vous voyez normalement le message `User 'Your Username' logged-in`.
1. Sélectionnez le bouton **Appeler l’API**. L’application monopage envoie le jeton d’accès dans une demande à l’API web protégée, qui renvoie le nom d’affichage de l’utilisateur connecté :

    ![Application monopage dans le navigateur montrant le résultat JSON avec le nom d’utilisateur retourné par l’API](./media/configure-authentication-sample-spa-app/sample-app-result.png)

## <a name="deploy-your-application"></a>Déployer votre application 

Dans une application de production, l’URI de redirection de l’inscription de l’application est généralement un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com/signin-oidc`. 

Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier. Les restrictions suivantes s’appliquent aux URI de redirection :

* L’URL de réponse doit commencer par le schéma `https`.
* L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’[exemple de code](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)
* Découvrir comment utiliser les [options d’authentification dans votre propre application monopage à l’aide d’Azure AD B2C](enable-authentication-spa-app-options.md)
