---
title: Application à page unique (se connecter) - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (connexion)
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
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138805"
---
# <a name="single-page-application---sign-in"></a>Application à page unique - connexion

Découvrez comment ajouter une inscription dans le code pour votre application à page unique.

Avant de pouvoir obtenir des jetons pour accéder aux API dans votre application, vous devez un contexte de l’utilisateur authentifié. Vous pouvez connecter des utilisateurs à votre application dans MSAL.js de deux manières :

* [Connectez-vous à une fenêtre contextuelle](#sign-in-with-a-pop-up-window) à l’aide de `loginPopup` (méthode)
* [Connectez-vous avec redirection](#sign-in-with-redirect) à l’aide de `loginRedirect` (méthode)

Vous pouvez également passer les étendues de l’API pour laquelle vous avez besoin de l’utilisateur à donner son consentement au moment de la connexion.

> [!NOTE]
> Si votre application a déjà accès à un contexte de l’utilisateur authentifié ou un id de jeton, vous pouvez ignorer l’étape de connexion et acquérir directement des jetons. Pour plus d’informations, consultez [sso sans connexion de msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Choix entre une expérience de la fenêtre contextuelle ou de redirection

Vous ne pouvez pas utiliser une combinaison de méthodes de la fenêtre contextuelle et la redirection dans votre application. Le choix entre une expérience de la fenêtre contextuelle ou redirection dépend de votre flux de l’application.

* Si vous ne souhaitez pas l’utilisateur de quitter votre page principale de l’application lors de l’authentification, il est recommandé d’utiliser les méthodes contextuelles. Étant donné que la redirection de l’authentification se produit dans une fenêtre indépendante, l’état de l’application principale est conservé.

* Il existe certains cas où vous devrez peut-être utiliser les méthodes de redirection. Si les utilisateurs de votre application ont des contraintes de navigateur ou stratégies où windows de fenêtres contextuelles sont désactivées, vous pouvez utiliser les méthodes de redirection. Utilisez les méthodes de redirection avec un navigateur Internet Explorer dans la mesure où il existe certaines [problèmes connus avec Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) lors de la gestion des fenêtres publicitaires.

## <a name="sign-in-with-a-pop-up-window"></a>Connectez-vous à une fenêtre contextuelle

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

Le wrapper MSAL Angular vous permet de sécuriser des itinéraires spécifiques dans votre application en ajoutant simplement la `MsalGuard` à la définition d’itinéraire. Ce garde appellera la méthode de connexion lors de l’accès de cet itinéraire.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Pour une expérience de la fenêtre contextuelle, activer la `popUp` option de configuration. Vous pouvez également passer les étendues qui nécessitent le consentement comme suit :

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

## <a name="sign-in-with-redirect"></a>Connectez-vous avec la redirection

### <a name="javascript"></a>JavaScript

Les méthodes de redirection ne retournent pas une promesse en raison de la navigation de l’application principale. Pour traiter et les jetons retournés d’accès, vous devez enregistrer des rappels de réussite et d’erreur avant d’appeler les méthodes de redirection.

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

Le code ici est identique à celui décrit ci-dessus sous le signe avec une section de la fenêtre contextuelle. Le flux par défaut est de redirection.

> [!NOTE]
> Le jeton d’ID ne contient pas les étendues ayant reçu le consentement et ne représente que l’utilisateur authentifié. Les étendues de consentement sont retournés dans le jeton d’accès que vous allez acquérir à l’étape suivante.

## <a name="sign-out"></a>Se déconnecter

La bibliothèque MSAL fournit un `logout` méthode qui permet d’effacer le cache dans le stockage de navigateur et envoie une demande de déconnexion à Azure AD. Après la déconnexion, il redirige vers la page de démarrage d’application par défaut.

Vous pouvez configurer l’URI vers lequel il doit rediriger après connexion arrière en définissant le `postLogoutRedirectUri`. Cet URI doit également être enregistré comme l’URI Logout dans l’inscription de votre application.

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
> [L’acquisition d’un jeton pour l’application](scenario-spa-acquire-token.md)
