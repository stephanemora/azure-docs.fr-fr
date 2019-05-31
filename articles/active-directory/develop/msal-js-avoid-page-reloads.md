---
title: Éviter les rechargements de page (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: Découvrez comment éviter les rechargements de page lors de l’acquisition et le renouvellement de jetons en mode silencieux à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420467"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Éviter les rechargements de page lors de l’acquisition et le renouvellement de jetons en mode silencieux en utilisant MSAL.js
Bibliothèque d’authentification Microsoft pour les utilisations de JavaScript (MSAL.js) masquées `iframe` éléments à acquérir et renouveler les jetons en mode silencieux en arrière-plan. Azure AD retourne le jeton au redirect_uri inscrites que spécifié dans la demande de jeton (par défaut il s’agit de page racine de l’application). Dans la mesure où la réponse est un message 302, il en résulte le HTML correspondant à la `redirect_uri` chargées dans le `iframe`. Généralement l’application `redirect_uri` est la page racine et qui le force à recharger.

Dans d’autres cas, si la navigation vers la page racine de l’application nécessite une authentification, peut donner lieu à imbriquée `iframe` éléments ou `X-Frame-Options: deny` erreur.

Étant donné que MSAL.js ne peut pas faire disparaître la 302 émis par Azure AD et est nécessaire pour traiter le jeton retourné, il ne peut pas empêcher le `redirect_uri` de chargées dans le `iframe`.

Pour éviter toute l’application recharger à nouveau ou autres erreurs provoquées pour cette raison, suivez ces solutions de contournement.

## <a name="specify-different-html-for-the-iframe"></a>Spécifier différentes HTML pour l’iframe

Définir le `redirect_uri` propriété sur la configuration pour une page simple, qui ne nécessite pas d’authentification. Vous devez vous assurer qu’il correspond à la `redirect_uri` inscrit dans le portail Azure. Cela affectera pas expérience de connexion de l’utilisateur comme MSAL enregistre la page de démarrage lorsque l’utilisateur commence le processus de connexion et redirige vers l’emplacement exact, une fois que la connexion soit terminée.

## <a name="initialization-in-your-main-app-file"></a>Initialisation dans votre fichier d’application principale

Si votre application est structurée tel qu’il existe un fichier Javascript central qui définit l’initialisation de l’application, de routage et d’autres choses, vous pouvez charger conditionnelle de vos modules de l’application selon si l’application est chargé dans un `iframe` ou non. Exemple :

Dans AngularJS : app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

Dans Angular : app.module.TS

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent :

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [création d’une application monopage (SPA)](scenario-spa-overview.md) utilisant MSAL.js.