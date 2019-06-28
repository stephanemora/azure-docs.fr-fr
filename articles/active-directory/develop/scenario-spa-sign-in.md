---
title: Application monopage (connexion) - Plateforme d’identités Microsoft
description: En savoir plus sur la création d'une application monopage (connexion)
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
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138805"
---
# <a name="single-page-application---sign-in"></a>Application monopage - Connexion

Découvrez comment ajouter une connexion au code de votre application monopage.

Pour obtenir des jetons d'accès aux API de votre application, il vous faut un contexte d’utilisateur authentifié. Vous pouvez connecter des utilisateurs à votre application dans MSAL.js de deux manières :

* [Connexion avec une fenêtre indépendante ](#sign-in-with-a-pop-up-window) à l’aide de la méthode `loginPopup`
* [Connexion avec redirection](#sign-in-with-redirect) à l’aide de la méthode `loginRedirect`

Vous pouvez également passer les étendues des API pour lesquelles l'utilisateur doit donner son consentement au moment de la connexion.

> [!NOTE]
> Si votre application a déjà accès à un contexte d’utilisateur authentifié ou à un jeton d'ID, vous pouvez ignorer l’étape de connexion et obtenir directement des jetons. Pour plus d’informations, consultez [Authentification unique sans connexion msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Choix entre une expérience avec fenêtre indépendante ou avec redirection

Vous ne pouvez pas utiliser la combinaison de ces méthodes de fenêtre indépendante et de redirection dans votre application. Le choix entre la fenêtre indépendante et la redirection dépend du flux d'application.

* Si vous ne souhaitez pas que l’utilisateur quitte la page principale de votre application lors de l’authentification, il est recommandé d’utiliser la méthode avec fenêtre indépendante. La redirection de l’authentification se produisant dans une fenêtre indépendante, l’état de l’application principale est conservé.

* Dans certains cas, vous pouvez être amené à utiliser les méthodes avec redirection. Si les utilisateurs de votre application ont des contraintes imposées par le navigateur ou par des stratégies qui bloquent les fenêtres indépendantes, vous pouvez utiliser les méthodes avec redirection. Utilisez les méthodes avec redirection avec Internet Explorer, dans la mesure où il existe certains [problèmes connus avec Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) lors de la gestion des fenêtres indépendantes.

## <a name="sign-in-with-a-pop-up-window"></a>Se connecter avec une fenêtre indépendante

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
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

Le wrapper MSAL Angular vous permet de sécuriser des itinéraires spécifiques de votre application en ajoutant `MsalGuard` à la définition d’itinéraire. Cette protection appelle la méthode de connexion en cas d'accès à l'itinéraire.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Pour une expérience de fenêtre contextuelle, activez l'option de configuration `popUp`. Vous pouvez également passer les étendues nécessitant un consentement comme suit :

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Se connecter avec redirection

### <a name="javascript"></a>JavaScript

Les méthodes de redirection ne renvoient pas de promesse en raison de la navigation en dehors de l’application principale. Pour traiter et accéder aux jetons renvoyés, vous devez enregistrer les rappels de réussite et d’erreur avant d’appeler les méthodes de redirection.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Ce code est identique à celui décrit ci-dessus, sous la section ayant trait à la connexion avec fenêtre contextuelle. La redirection correspond au flux par défaut.

> [!NOTE]
> Le jeton d’ID ne contient pas les étendues autorisées et représente uniquement l’utilisateur authentifié. Les étendues autorisées sont renvoyées dans le jeton d’accès que vous obtenez à l’étape suivante.

## <a name="sign-out"></a>Se déconnecter

La bibliothèque MSAL fournit une méthode `logout` qui permet d’effacer le cache dans le stockage de navigateur et envoie une requête de déconnexion à Azure AD. Après déconnexion, elle redirige vers la page de démarrage d’application par défaut.

Vous pouvez configurer l’URI de redirection après déconnexion en définissant `postLogoutRedirectUri`. Cet URI doit également être enregistré en tant qu'URI de déconnexion dans l'inscription de votre application.

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
