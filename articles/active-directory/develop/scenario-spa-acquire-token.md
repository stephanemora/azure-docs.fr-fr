---
title: Application à page unique (acquérir un jeton pour appeler une API) - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (acquérir un jeton pour appeler une API)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138815"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Application à page unique - acquérir un jeton pour appeler une API

Le modèle pour l’acquisition des jetons d’API avec MSAL.js consiste à tentent d’abord une demande de jeton en mode silencieux à l’aide de la `acquireTokenSilent` (méthode). Lorsque cette méthode est appelée, la bibliothèque vérifie d’abord le cache dans le stockage de navigateur pour voir si un jeton valide existe et le retourne. Lorsqu’il n’existe aucun jeton valide dans le cache, il envoie une demande de jeton en mode silencieux à Azure Active Directory (Azure AD) à partir d’un iframe masqué. Cette méthode permet également de la bibliothèque de renouveler les jetons. Pour plus d’informations sur la session d’authentification unique et des valeurs de durée de vie dans Azure AD, consultez [des durées de vie du jeton](active-directory-configurable-token-lifetimes.md).

Les demandes de jeton en mode silencieux à Azure AD peuvent échouer pour des raisons telles que la session Active Directory Azure a expiré ou une modification de mot de passe. Dans ce cas, vous pouvez appeler une des méthodes interactives (qui invitent l’utilisateur) pour acquérir des jetons.

* [Acquérir le jeton avec une fenêtre contextuelle](#acquire-token-with-a-pop-up-window) à l’aide de `acquireTokenPopup`
* [Acquérir le jeton avec redirection](#acquire-token-with-redirect) à l’aide de `acquireTokenRedirect`

**Choix entre une expérience de la fenêtre contextuelle ou de redirection**

 Vous ne pouvez pas utiliser une combinaison de méthodes de la fenêtre contextuelle et la redirection dans votre application. Le choix entre une expérience de la fenêtre contextuelle ou redirection dépend de votre flux de l’application.

* Si vous ne souhaitez pas l’utilisateur de quitter votre page principale de l’application lors de l’authentification, il est recommandé d’utiliser les méthodes contextuelles. Étant donné que la redirection de l’authentification se produit dans une fenêtre indépendante, l’état de l’application principale est conservé.

* Il existe certains cas où vous devrez peut-être utiliser les méthodes de redirection. Si les utilisateurs de votre application ont des contraintes de navigateur ou stratégies où windows de fenêtres contextuelles sont désactivées, vous pouvez utiliser les méthodes de redirection. Il est également recommandé d’utiliser les méthodes de redirection avec un navigateur Internet Explorer dans la mesure où il existe certaines [problèmes connus avec Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) lors de la gestion des fenêtres publicitaires.

Vous pouvez définir les étendues d’API que vous souhaitez que le jeton d’accès à inclure lors de la création de la demande de jeton d’accès. Notez que toutes les étendues demandées ne peut pas être accordées dans le jeton d’accès et varie selon le consentement de l’utilisateur.

## <a name="acquire-token-with-a-pop-up-window"></a>Acquérir le jeton avec une fenêtre contextuelle

### <a name="javascript"></a>JavaScript

Le modèle ci-dessus à l’aide de méthodes pour une expérience contextuelle :

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

Le wrapper MSAL Angular fournit la commodité de l’ajout de l’intercepteur HTTP `MsalInterceptor` qui sera automatiquement acquérir des jetons d’accès en mode silencieux et joignez-les aux requêtes HTTP aux API.

Vous pouvez spécifier les étendues pour les API dans le `protectedResourceMap` option de configuration qui demande le MsalInterceptor lors de l’acquisition de jetons automatiquement.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Pour la réussite et d’échec de l’acquisition de jeton en mode silencieux, MSAL Angular fournit des rappels, à que vous pouvez vous abonner. Il est également important de se rappeler annuler l’abonnement.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Ou bien, vous pouvez également explicitement acquérir des jetons à l’aide des méthodes d’acquisition de jeton comme décrit dans la bibliothèque de MSAL.js principale.

## <a name="acquire-token-with-redirect"></a>Acquérir le jeton avec redirection

### <a name="javascript"></a>JavaScript

Le modèle est comme décrit ci-dessus, mais il est indiqué avec une méthode de redirection pour acquérir le jeton de manière interactive. Notez que vous devez enregistrer le rappel de redirection comme indiqué ci-dessus.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

Cela est identique à celui décrit ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appeler une API Web](scenario-spa-call-api.md)
