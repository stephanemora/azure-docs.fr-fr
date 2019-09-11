---
title: Application à page unique (acquérir un jeton pour appeler une API) - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (acquérir un jeton pour appeler une API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135706"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Application à page unique - acquérir un jeton pour appeler une API

Le modèle pour l’acquisition des jetons des API avec MSAL.js consiste à tenter d’abord une demande de jeton en mode silencieux à l’aide de la méthode `acquireTokenSilent`. Lorsque cette méthode est appelée, la bibliothèque vérifie d’abord le cache dans le stockage de navigateur pour voir si un jeton valide existe et le retourne. Lorsqu’il n’y a aucun jeton valide dans le cache, elle envoie une demande de jeton en mode silencieux à Azure Active Directory (Azure AD) à partir d’un iframe masqué. Cette méthode permet également à la bibliothèque de renouveler les jetons. Pour plus d’informations sur la session d’authentification unique et les valeurs de durée de vie des jetons dans Azure AD, consultez [Durée de vie des jetons](active-directory-configurable-token-lifetimes.md).

Les demandes de jeton en mode silencieux à Azure AD peuvent échouer pour certaines raisons, par exemple le fait que la session Active Directory Azure a expiré ou que le mot de passe a été modifié. Dans ce cas, vous pouvez appeler une des méthodes interactives (qui invitent l’utilisateur) pour acquérir des jetons.

* [Acquérir le jeton avec une fenêtre indépendante ](#acquire-token-with-a-pop-up-window) à l’aide de `acquireTokenPopup`
* [Acquérir le jeton avec redirection](#acquire-token-with-redirect) à l’aide de `acquireTokenRedirect`

**Choix entre une expérience avec fenêtre indépendante ou avec redirection**

 Vous ne pouvez pas utiliser la combinaison de ces méthodes de fenêtre indépendante et de redirection dans votre application. Le choix entre la fenêtre indépendante et la redirection dépend du flux d'application.

* Si vous ne souhaitez pas que l’utilisateur quitte la page principale de votre application lors de l’authentification, il est recommandé d’utiliser la méthode avec fenêtre indépendante. La redirection de l’authentification se produisant dans une fenêtre indépendante, l’état de l’application principale est conservé.

* Dans certains cas, vous pouvez être amené à utiliser les méthodes avec redirection. Si les utilisateurs de votre application ont des contraintes imposées par le navigateur ou par des stratégies qui bloquent les fenêtres indépendantes, vous pouvez utiliser les méthodes avec redirection. Il est également recommandé d’utiliser les méthodes avec redirection avec Internet Explorer, dans la mesure où il existe certains [problèmes connus avec Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) lors de la gestion des fenêtres indépendantes.

Vous pouvez définir les étendues d’API que vous souhaitez que le jeton d’accès inclue lors de la création de la demande de jeton d’accès. Notez que toutes les étendues demandées peuvent ne pas être accordées dans le jeton d’accès et varient en fonction du consentement de l’utilisateur.

## <a name="acquire-token-with-a-pop-up-window"></a>Acquérir le jeton avec une fenêtre indépendante

### <a name="javascript"></a>JavaScript

Le modèle ci-dessus avec les méthodes pour une expérience avec fenêtre indépendante :

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

Le wrapper MSAL Angular permet d’ajouter l’intercepteur HTTP qui acquiert automatiquement des jetons d’accès en mode silencieux et les joint aux requêtes HTTP aux API.

Vous pouvez spécifier les étendues des API dans l’option de configuration `protectedResourceMap` que demande MsalInterceptor lors de l’acquisition de jetons en mode automatique.

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

Pour la réussite et d’échec de l’acquisition de jetons en mode silencieux, MSAL Angular fournit des rappels auxquels vous pouvez vous abonner. Il est également important de se souvenir d’annuler l’abonnement.

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

Sinon, vous pouvez également explicitement acquérir des jetons à l’aide des méthodes d’acquisition de jeton comme décrit dans la bibliothèque MSAL.js principale.

## <a name="acquire-token-with-redirect"></a>Acquérir le jeton avec redirection

### <a name="javascript"></a>JavaScript

Le modèle est tel que décrit ci-dessus, mais il est présenté avec une méthode de redirection permettant d’acquérir le jeton de manière interactive. Vous devez inscrire le rappel de redirection comme indiqué ci-dessus.

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

## <a name="request-for-optional-claims"></a>Demande de revendications facultatives
Vous pouvez demander des revendications facultatives dans votre application pour spécifier les revendications supplémentaires à inclure dans les jetons de votre application. Pour demander des revendications facultatives dans id_token, vous pouvez envoyer un objet de revendications converti en chaînes au champ claimsRequest de la classe AuthenticationParameters.ts.

Vous pouvez utiliser des revendications facultatives aux fins suivantes :

- Inclure des revendications supplémentaires dans les jetons pour votre application.
- Modifier le comportement de certaines revendications retournées par Azure AD dans les jetons.
- Ajouter et accéder à des revendications personnalisées pour votre application.


### <a name="javascript"></a>JavaScript
```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```
Pour en savoir plus sur les revendications facultatives, consultez [Revendications facultatives](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

Identique à ce qui est décrit ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Appel d’une API web](scenario-spa-call-api.md)
