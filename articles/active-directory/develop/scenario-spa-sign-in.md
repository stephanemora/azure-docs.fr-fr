---
title: Connexion à une application monopage - Plateforme d’identités Microsoft | Azure
description: Apprendre à créer une application monopage (connexion)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d2494a7ad89c9e9b3a525ddd04290a08eb3af58
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962421"
---
# <a name="single-page-application-sign-in"></a>Application monopage : Connexion

Découvrez comment ajouter une connexion au code de votre application monopage.

Pour obtenir des jetons d’accès aux API de votre application, vous avez besoin d’un contexte d’utilisateur authentifié. Vous pouvez connecter des utilisateurs à votre application dans MSAL.js de deux manières :

* [Fenêtre contextuelle](#sign-in-with-a-pop-up-window), au moyen de la méthode `loginPopup`
* [Redirection](#sign-in-with-redirect), au moyen de la méthode `loginRedirect`

Vous pouvez également passer les étendues des API, pour lesquelles l’utilisateur doit donner son consentement au moment de la connexion.

> [!NOTE]
> Si votre application a déjà accès à un contexte d’utilisateur authentifié ou à un jeton d’ID, vous pouvez ignorer l’étape de connexion et obtenir directement des jetons. Pour obtenir des informations détaillées, consultez [Authentification unique sans connexion via msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Choix entre une expérience avec fenêtre indépendante ou avec redirection

Vous ne pouvez pas utiliser conjointement ces deux méthodes de fenêtre contextuelle et de redirection dans votre application. Le choix entre la fenêtre contextuelle et la redirection dépend du flux de votre application :

* Si vous ne souhaitez pas que les utilisateurs quittent la page principale de votre application lors de l’authentification, nous vous recommandons d’utiliser la méthode avec fenêtre contextuelle. La redirection de l’authentification se produisant dans une fenêtre contextuelle, l’état de l’application principale est conservé.

* Si les utilisateurs sont soumis à des contraintes imposées par le navigateur ou par des stratégies qui bloquent les fenêtres contextuelles, vous pouvez utiliser la méthode avec redirection. Utilisez la méthode avec redirection dans le navigateur Internet Explorer, car il existe des [problèmes connus avec l’utilisation de fenêtres contextuelles dans Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Se connecter avec une fenêtre contextuelle

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

Le wrapper MSAL Angular vous permet de sécuriser des itinéraires spécifiques dans votre application en ajoutant `MsalGuard` à la définition d’itinéraire. Cette protection appelle la méthode de connexion en cas d'accès à l'itinéraire.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Pour une expérience de fenêtre contextuelle, activez l’option de configuration `popUp`. Vous pouvez également passer les étendues nécessitant un consentement comme suit :

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Se connecter avec une redirection

### <a name="javascript"></a>JavaScript

Les méthodes de redirection ne retournent pas de promesse en raison du déplacement en dehors de l’application principale. Pour traiter et accéder aux jetons retournés, vous devez enregistrer les rappels de réussite et d’erreur avant d’appeler les méthodes de redirection.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Ce code est identique à celui décrit plus haut, à la section traitant de la connexion au moyen d’une fenêtre contextuelle. La redirection correspond au flux par défaut.

> [!NOTE]
> Le jeton d’ID ne contient pas les étendues autorisées et représente uniquement l’utilisateur authentifié. Les étendues acceptées sont retournées dans le jeton d’accès que vous obtenez à l’étape suivante.

## <a name="sign-out"></a>Se déconnecter

La bibliothèque MSAL fournit une méthode `logout` qui efface le cache dans le stockage de navigateur, et envoie une requête de déconnexion à Azure Active Directory (Azure AD). Après la déconnexion, la bibliothèque redirige vers la page de démarrage par défaut d’une application.

Vous pouvez configurer l’URI de redirection après la déconnexion en définissant `postLogoutRedirectUri`. Cet URI doit également être enregistré en tant qu’URI de déconnexion dans l’inscription de votre application.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Acquisition d’un jeton pour l’application](scenario-spa-acquire-token.md)
