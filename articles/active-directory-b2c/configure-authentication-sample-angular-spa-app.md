---
title: Configurer l’authentification dans un exemple d’application monopage Angular à l’aide d’Azure Active Directory B2C
description: Découvrez comment utiliser Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application monopage Angular.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3d7a519a982653693254288e02ffdb867e70f78f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572615"
---
# <a name="configure-authentication-in-a-sample-angular-single-page-application-by-using-azure-active-directory-b2c"></a>Configurer l’authentification dans un exemple d’application monopage Angular à l’aide d’Azure Active Directory B2C

Cet article utilise un exemple d’application monopage Angular pour illustrer l’ajout d’une authentification Azure Active Directory B2C (Azure AD B2C) à vos applications monopages Angular.

## <a name="overview"></a>Vue d’ensemble

OpenID Connect (OIDC) est un protocole d’authentification basé sur OAuth 2.0, que vous pouvez utiliser pour connecter de façon sécurisée un utilisateur à une application. Cet exemple Angular utilise [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular) et le [Navigateur MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser). MSAL est une bibliothèque fournie par Microsoft qui simplifie l’ajout d’une prise en charge de l’authentification et de l’autorisation aux applications monopages Angular.

### <a name="sign-in-flow"></a>Flux de connexion

Le flux de connexion implique les étapes suivantes :

1. L’utilisateur ouvre l’application et sélectionne **Connexion**. 
1. L’application lance une demande d’authentification et redirige l’utilisateur vers Azure AD B2C.
1. L’utilisateur [s’inscrit ou se connecte](add-sign-up-and-sign-in-policy.md) et [réinitialise le mot de passe](add-password-reset-policy.md) ou se connecte avec un [compte social](add-identity-provider.md).
1. Une fois la connexion établie, Azure AD B2C renvoie un code d’autorisation à l’application. L’application :
   1. échange le code d’autorisation contre un jeton d’ID, un jeton d’accès et un jeton d’actualisation ;
   1. lit les revendications du jeton d’ID ;
   1. stocke le jeton d’accès et le jeton d’actualisation dans un cache en mémoire pour une utilisation ultérieure. Le jeton d’accès permet à l’utilisateur d’appeler des ressources protégées, telles qu’une API web. Le jeton d’actualisation est utilisé pour acquérir un nouveau jeton d’accès.

### <a name="app-registration"></a>Inscription d'application

Pour permettre à votre application de se connecter avec Azure AD B2C et d’appeler une API web, vous devez inscrire deux applications dans le répertoire d’Azure AD B2C :  

- L’inscription de l’*Application monopage* (Angular) permet à votre application de se connecter avec Azure AD B2C. Pendant l’inscription de l’application, vous spécifiez l’*URI de redirection*. L’URI de redirection est le point de terminaison vers lequel l’utilisateur est redirigé après s’être authentifié avec Azure AD B2C. Le processus d’inscription de l’application génère un *ID d’application*, également appelé *ID client*, qui identifie votre application de façon unique. Cet article utilise l’exemple d’**ID d’application : 1**.

- L’inscription de *l’API web* permet à votre application d’appeler une API web protégée. Elle expose les autorisations de l’API web (étendues). Le processus d’inscription de l’application génère un ID d’application qui identifie votre API web de façon unique. Cet article utilise l’exemple d’**ID d’application : 2**. Accordez à votre application (**ID d’application : 1**) des autorisations sur les étendues de l’API web (**ID d’application : 2**).  

Le schéma suivant décrit les inscriptions d’applications et l’architecture de l’application.

![Schéma décrivant une application à page unique avec API web, inscriptions et jetons.](./media/configure-authentication-sample-angular-spa-app/spa-app-with-api-architecture.png) 

### <a name="call-to-a-web-api"></a>Appel à une API web

[!INCLUDE [active-directory-b2c-app-integration-call-api](../../includes/active-directory-b2c-app-integration-call-api.md)]

### <a name="sign-out-flow"></a>Flux de déconnexion

[!INCLUDE [active-directory-b2c-app-integration-sign-out-flow](../../includes/active-directory-b2c-app-integration-sign-out-flow.md)]

## <a name="prerequisites"></a>Prérequis

Avant de suivre les procédures décrites dans cet article, assurez-vous que votre ordinateur utilise :

* [Visual Studio Code](https://code.visualstudio.com/) ou un autre éditeur de code.
* [Runtime Node.js](https://nodejs.org/en/download/) et [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
* [CLI Angular](https://angular.io/cli).

## <a name="step-1-configure-your-user-flow"></a>Étape 1 : Configurer votre flux d’utilisateurs

[!INCLUDE [active-directory-b2c-app-integration-add-user-flow](../../includes/active-directory-b2c-app-integration-add-user-flow.md)]

## <a name="step-2-register-your-angular-spa-and-api"></a>Étape 2 : Inscrire votre application monopage Angular et votre API

Lors de cette étape, vous allez créer les inscriptions pour l’application monopage Angular et l’application API web. Vous spécifiez également les étendues de votre API web.

### <a name="21-register-the-web-api-application"></a>2.1 Inscrire l’application API web

[!INCLUDE [active-directory-b2c-app-integration-register-api](../../includes/active-directory-b2c-app-integration-register-api.md)]

### <a name="22-configure-scopes"></a>2.2 Configurer des étendues

[!INCLUDE [active-directory-b2c-app-integration-api-scopes](../../includes/active-directory-b2c-app-integration-api-scopes.md)]

### <a name="23-register-the-angular-app"></a>2.3 Inscrire l’application Angular

Suivez les étapes suivantes pour créer l’inscription d’application Angular :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à bien utiliser l’annuaire qui contient votre locataire Azure AD B2C. Sélectionnez l’icône **Répertoires + abonnements** dans la barre d’outils du portail.
1. Sur la page **Paramètres du portail | Répertoires + abonnements**, recherchez votre répertoire AD B2C Azure dans la liste **Nom de répertoire**, puis sélectionnez **Basculer**.
1. Dans le portail Azure, recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Inscriptions d’applications**, puis **Nouvelle inscription**.
1. Dans **Nom**, entrez le nom de l’application. Par exemple, entrez **MonApp**.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un fournisseur d’identité ou annuaire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . 
1. Sous **URI de redirection**, sélectionnez **Application monopage (SPA)** , puis entrez `http://localhost:4200` dans la zone de l’URL.
1. Sous **Autorisations**, cochez la case **Accorder le consentement administrateur aux autorisations openid et offline access**.
1. Sélectionnez **Inscription**.
1. Enregistrez la valeur de l’**ID d’application (client)** que vous utiliserez ultérieurement pour configurer l’application web.
    ![Capture d’écran montrant comment récupérer l’ID d’application Angular.](./media/configure-authentication-sample-angular-spa-app/get-azure-ad-b2c-app-id.png)  

### <a name="25-grant-permissions"></a>2.5 Accorder des autorisations

[!INCLUDE [active-directory-b2c-app-integration-grant-permissions](../../includes/active-directory-b2c-app-integration-grant-permissions.md)]

## <a name="step-3-get-the-angular-sample-code"></a>Étape 3 : Obtenir l’exemple de code Angular

Cet exemple montre la façon dont une application monopage Angular peut utiliser Azure AD B2C pour l’inscription et la connexion des utilisateurs. L’application acquiert ensuite un jeton d’accès et appelle une API web protégée.

  [Téléchargez un fichier .zip](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/archive/refs/heads/main.zip) de l’exemple ou clonez l’exemple à partir du [référentiel GitHub](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/) à l’aide de la commande suivante :

  ```
  git clone https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial.git
  ```

### <a name="31-configure-the-angular-sample"></a>3.1 Configurer l’exemple Angular

Maintenant que vous avez obtenu l’exemple d’application monopage, mettez à jour le code avec les valeurs d’Azure AD B2C et de l’API web. Dans le dossier de l’exemple, sous *src/App*, ouvrez le fichier *auth-config.ts*. Mettez à jour les clés avec les valeurs correspondantes :  


|Section  |Clé  |Valeur  |
|---------|---------|---------|
| b2cPolicies | noms |Flux d’utilisateur ou stratégie personnalisée que vous avez créée à l’[étape 1](#step-1-configure-your-user-flow). |
| b2cPolicies | authorities | Remplacez `your-tenant-name` par le [nom de votre locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple, utilisez `contoso.onmicrosoft.com`. Ensuite, remplacez le nom de stratégie par le flux d’utilisateur ou la stratégie personnalisée que vous avez créée à l’[étape 1](#step-1-configure-your-user-flow). Par exemple : `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>`. |
| b2cPolicies | authorityDomain|Votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `contoso.onmicrosoft.com`. |
| Configuration | clientId | ID d’application Angular de l’[étape 2.3](#23-register-the-angular-app). |
| protectedResources| endpoint| URL de l’API web : `http://localhost:5000/api/todolist`. |
| protectedResources| étendues| Étendues d’API web que vous avez créées à l’[étape 2.2](#22-configure-scopes). Par exemple : `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/tasks-api/tasks.read"]`. |

Le code obtenu *src/app/auth-config.ts* doit ressembler à l’exemple suivant :

```typescript
export const b2cPolicies = {
     names: {
         signUpSignIn: "b2c_1_susi_reset_v2",
         editProfile: "b2c_1_edit_profile_v2"
     },
     authorities: {
         signUpSignIn: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_susi_reset_v2",
         },
         editProfile: {
             authority: "https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/b2c_1_edit_profile_v2"
         }
     },
     authorityDomain: "your-tenant-name.b2clogin.com"
 };
 
 
export const msalConfig: Configuration = {
     auth: {
         clientId: '<your-MyApp-application-ID>',
         authority: b2cPolicies.authorities.signUpSignIn.
         knownAuthorities: [b2cPolicies.authorityDomain],
         redirectUri: '/', 
     },
    // More configuration here
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
```

## <a name="step-4-get-the-web-api-sample-code"></a>Étape 4 : Obtenir le code de l’exemple d’API web

Maintenant que l’API web est inscrite et que vous avez défini ses étendues, configurez le code de l’API web pour qu’il fonctionne avec votre locataire Azure AD B2C.

[Téléchargez une \*archive .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clonez l’exemple de projet d’API web à partir de GitHub. Vous pouvez également accéder directement au projet [Azure-Samples/active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) sur GitHub à l’aide de la commande suivante :

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="41-configure-the-web-api"></a>4.1 Configurer l’API web

Dans l’exemple de dossier, ouvrez le fichier *config.json*. Ce fichier contient des informations sur votre fournisseur d’identité Azure AD B2C. L’application API web les utilise pour valider le jeton d’accès transmis comme jeton du porteur par l’application web. Mettez à jour les propriétés suivantes des paramètres de l’application :

|Section  |Clé  |Valeur  |
|---------|---------|---------|
|credentials|tenantName| La première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C. Par exemple : `contoso`.|
|credentials|clientID| ID d’application de l’API web de l’[étape 2.1](#21-register-the-web-api-application). Dans le [schéma précédent](#app-registration), il s’agit de l’application avec l’**ID d’application : 2**.|
|credentials| émetteur| Valeur de la revendication `iss` de l’émetteur de jeton. Par défaut, Azure AD B2C renvoie le jeton au format suivant : `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`. Remplacez `<your-tenant-name>` par la première partie de votre [nom de locataire](tenant-management.md#get-your-tenant-name) Azure AD B2C, et `<your-tenant-ID>` par votre [ID de locataire Azure AD B2C](tenant-management.md#get-your-tenant-id). |
|stratégies|policyName|Flux d’utilisateur ou stratégie personnalisée que vous avez créée à l’[étape 1](#step-1-configure-your-user-flow). Si votre application utilise plusieurs flux d’utilisateur ou stratégies personnalisées, spécifiez-en un seul. Par exemple, utilisez le flux d’utilisateur d’inscription ou de connexion.|
| resource| scope | Étendues de l’inscription de votre application API web de l’[étape 2.5](#25-grant-permissions). |

Votre fichier config final doit ressembler au JSON suivant :

```json
{
    "credentials": {
        "tenantName": "<your-tenant-namee>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"] 
    },
    // More settings here
} 
```

## <a name="step-5-run-the-angular-spa-and-web-api"></a>Étape 5 : Exécuter l’application monopage Angular et l’API web

Vous êtes maintenant prêt à tester l’accès délimité d’Angular à l’API. Dans cette étape, exécutez l’API web et l’exemple d’application Angular sur votre ordinateur local. Ensuite, connectez-vous à l’application Angular, puis sélectionnez le bouton **TodoList** pour envoyer une demande à l’API protégée.

### <a name="run-the-web-api"></a>Exécuter l’API web

1. Ouvrez une fenêtre de console et accédez au répertoire qui contient l’exemple d’API web. Par exemple :

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    node index.js
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application :

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-angular-application"></a>Exécuter l’application Angular

1. Ouvrez une autre fenêtre de console, puis accédez au répertoire contenant l’exemple Angular. Par exemple :

    ```console
    cd ms-identity-javascript-angular-tutorial-main/3-Authorization-II/2-call-api-b2c/SPA
    ```

1. Exécutez les commandes suivantes :

    ```console
    npm install && npm update
    npm start
    ```

    La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application :

    ```console
    Listening on port 4200...
    ```

1. Accédez à `http://localhost:4200` dans votre navigateur pour voir l’application.
1. Sélectionnez **Connexion**.

    ![Capture d’écran montrant l’exemple d’application Angular avec le lien de connexion.](./media/configure-authentication-sample-angular-spa-app/sample-app-sign-in.png)

1. Terminez le processus d’inscription ou de connexion.
1. Une fois la connexion établie, vous devriez voir votre profil s’afficher. Dans le menu, sélectionnez **ToDoList**.

    ![Capture d’écran montrant l’exemple d’application Angular avec le profil utilisateur et l’appel à la liste des tâches.](./media/configure-authentication-sample-angular-spa-app/sample-app-result.png)

1. Sélectionnez **Ajouter** pour ajouter de nouveaux éléments à la liste ou utilisez les icônes pour supprimer ou modifier des éléments.

    ![Capture d’écran montrant l’appel de l’exemple d’application Angular à la liste des tâches.](./media/configure-authentication-sample-angular-spa-app/sample-app-calls-web-api.png)

## <a name="deploy-your-application"></a>Déployer votre application 

Dans une application de production, l’URI de redirection de l’inscription de l’application est généralement un point de terminaison accessible publiquement dans lequel votre application s’exécute, comme `https://contoso.com`. 

Vous pouvez ajouter des URI de redirection à vos applications inscrites à tout moment et les modifier. Les restrictions suivantes s’appliquent aux URI de redirection :

* L’URL de réponse doit commencer par le schéma `https`.
* L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur l’exemple de code](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/)
* [Activer l’authentification dans votre propre application Angular](enable-authentication-angular-spa-app.md)
* [Configurer les options d’authentification dans votre application Angular](enable-authentication-angular-spa-app-options.md)
* [Activer l’authentification dans votre propre API web](enable-authentication-web-api.md)
