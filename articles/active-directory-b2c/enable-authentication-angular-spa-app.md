---
title: Activer l’authentification dans une application Angular à l’aide de blocs de construction Azure Active Directory B2C.
description: La compilation de blocs de d’Azure Active Directory B2C pour connecter et inscrire des utilisateurs dans une application Angular.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 6c8ab2ced9dc81594e13f574060050ade1b6e09a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524052"
---
# <a name="enable-authentication-in-your-own-angular-application-using-azure-active-directory-b2c"></a>Activer l’authentification dans votre propre application Angular à l’aide d’Azure Active Directory B2C

Cet article explique comment ajouter l’authentification Azure Active Directory B2C (Azure AD B2C) à votre propre application monopage Angular (SPA). Apprenez à intégrer une application Angular à la bibliothèque d’authentification [MSAL pour Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular). 

Utilisez cet article avec l’article [Configurer l’authentification dans un exemple d’application SPA Angular](./configure-authentication-sample-angular-spa-app.md), en remplaçant l’exemple d’application Angular par votre propre application Angular. Une fois que vous aurez suivi les étapes décrites dans cet article, votre application acceptera les connexions via Azure AD B2C.

## <a name="prerequisites"></a>Configuration requise

Passez en revue les conditions préalables et les étapes d’intégration dans l’article [Configurer l’authentification dans un exemple d’application SPA Angular](configure-authentication-sample-angular-spa-app.md).

## <a name="create-an-angular-app-project"></a>Créer un projet d’application Angular

Vous pouvez utiliser un projet d’application Angular existant ou en créer un. Pour créer un projet, exécutez les commandes suivantes. 

Les commandes suivantes permettent :

1. D’installer la [CLI Angular](https://angular.io/cli) à l’aide d’un gestionnaire de package npm.
1. De [créer un espace de travail Angular](https://angular.io/cli/new) avec un module de routage. Le nom de l’application est `msal-angular-tutorial`, vous pouvez le remplacer par tout autre nom d’application Angular valide, comme `contoso-car-service`.
1. D’ouvrir le dossier du répertoire de l’application.

```
npm install -g @angular/cli 
ng new msal-angular-tutorial --routing=true --style=css --strict=false
cd msal-angular-tutorial
```

## <a name="install-the-dependencies"></a>Installer les dépendances

Pour installer le [navigateur MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-browser) et les bibliothèques [Angular MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/master/lib/msal-angular) dans votre application, dans votre interpréteur de commandes, exécutez les commandes suivantes :

```
npm install @azure/msal-browser @azure/msal-angular
```

Installez la [bibliothèque de composants Material Angular](https://material.angular.io/) (facultatif, pour IU).

```
npm install @angular/material @angular/cdk
```

## <a name="add-the-authentication-components"></a>Ajouter les composants d’authentification

L’exemple de code est constitué des composants suivants : 

|Composant  |Type  |Description  |
|---------|---------|---------|
| auth-config.ts| Constantes | Fichier de configuration qui contient des informations sur votre fournisseur d’identité Azure AD B2C et le service API Web. L’application Angular utilise ces informations pour établir une relation de confiance avec Azure AD B2C, connecter et déconnecter l’utilisateur, acquérir des jetons et les valider. |
| app.module.ts| [Module Angular](https://angular.io/guide/architecture-modules)| Décrit la façon dont les parties de l’application s’assemblent. Il s’agit du module racine utilisé pour le démarrage et le lancement de l’application. Dans cette procédure pas à pas, vous allez ajouter des composants au module *app.module.ts* et démarrer la bibliothèque MSAL avec l’objet MSAL config.  |
| app-routing.module.ts | [Module de routage Angular](https://angular.io/tutorial/toh-pt5) | Active la navigation en interprétant une URL de navigateur et en chargeant le composant correspondant.  Dans cette procédure pas à pas, vous allez ajouter des composants au module de routage et protéger les composants avec [MSAL Guard](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-guard.md). Seuls les utilisateurs autorisés peuvent accéder aux composants protégés.   |
| app.component.* | [Composant Angular](https://angular.io/guide/architecture-components) | La commande `ng new` a créé un projet Angular avec un composant racine. Dans cette procédure pas à pas, vous allez modifier le composant d’application pour héberger la barre de navigation supérieure. La barre de navigation contient différents boutons, y compris ceux de connexion et déconnexion. La classe *app.component.ts* gère les événements de connexion et de déconnexion.  |
| home.component.* | [Composant Angular](https://angular.io/guide/architecture-components)|Dans cette procédure pas à pas, vous allez ajouter le composant *home* pour afficher la page d’accueil d’accès anonyme. Ce composant montre comment vérifier si un utilisateur s’est connecté.  |
| profile.component.* | [Composant Angular](https://angular.io/guide/architecture-components) | Dans cette procédure pas à pas, vous allez ajouter le composant *profile* pour apprendre à lire les revendications de jeton d’ID. |
| webapi.component.* | [Composant Angular](https://angular.io/guide/architecture-components)| Dans cette procédure pas à pas, vous allez ajouter le composant *webapi* pour apprendre à appeler une API web. |



Pour ajouter les composants suivants à votre application, exécutez les commandes de CLI Angular suivantes. Les commandes `generate component` permettent :

1. De créer un dossier pour chaque composant. Le dossier contient les fichiers TypeScript, HTML, CSS et test. 
1. De mettre à jour les fichiers `app.module.ts` et `app-routing.module.ts` avec des références aux nouveaux composants. 

```
ng generate component home
ng generate component profile
ng generate component webapi
```

## <a name="add-the-app-settings"></a>Ajouter les paramètres de l’application

Les paramètres du fournisseur d’identité Azure AD B2C et de l’API web sont stockés dans le fichier `auth-config.ts`. Dans votre dossier *src/app*, créez un fichier nommé *auth-config.ts* contenant le code suivant. Ensuite, modifiez les paramètres selon la description de [3.1 Configurer l’exemple Angular](configure-authentication-sample-angular-spa-app.md#31-configure-the-angular-sample).

```typescript
import { LogLevel, Configuration, BrowserCacheLocation } from '@azure/msal-browser';

const isIE = window.navigator.userAgent.indexOf("MSIE ") > -1 || window.navigator.userAgent.indexOf("Trident/") > -1;
 
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
     cache: {
         cacheLocation: BrowserCacheLocation.LocalStorage,.
         storeAuthStateInCookie: isIE, 
     },
     system: {
         loggerOptions: {
            loggerCallback: (logLevel, message, containsPii) => {
                console.log(message);
             },
             logLevel: LogLevel.Verbose,
             piiLoggingEnabled: false
         }
     }
 }

export const protectedResources = {
  todoListApi: {
    endpoint: "http://localhost:5000/api/todolist",
    scopes: ["https://your-tenant-namee.onmicrosoft.com/api/tasks.read"],
  },
}
export const loginRequest = {
  scopes: []
};
```

## <a name="initiate-the-authentication-libraries"></a>Ajouter les bibliothèques d’authentification

Les applications clientes publiques ne sont pas approuvées pour conserver en toute sécurité les secrets d’application et ne disposent donc pas de clé secrète client. Dans le dossier *src/app*, ouvrez *app.module.ts* et effectuez les modifications suivantes :

1. Importez les bibliothèques MSAL et navigateur MSAL.
1. Importez le module de configuration Azure AD B2C.
1. Importez `HttpClientModule`. Le client HTTP est utilisé pour appeler des API Web.
1. Importez l’intercepteur HTTP Angular. MSAL utilise l’intercepteur pour injecter le jeton du porteur dans l’en-tête d’autorisation HTTP.
1. Ajoutez les matériaux Angular essentiels.
1. Instanciez MSAL à l’aide de l’objet d’application client public à plusieurs comptes. L’initialisation de MSAL comprend la transmission des éléments suivants :
    1. L’objet de configuration *auth-config.ts*.
    1. L’objet de configuration de protection du routage.
    1. L’objet de configuration de l’intercepteur MSAL. La classe d’intercepteur acquiert automatiquement des jetons pour les requêtes sortantes qui utilisent [HttpClient](https://angular.io/api/common/http/HttpClient) Angular pour les ressources connues protégées.
1. Configurez les [fournisseurs Angular](https://angular.io/guide/providers) `HTTP_INTERCEPTORS` et `MsalGuard`.  
1. Ajoutez `MsalRedirectComponent` au [démarrage Angular](https://angular.io/guide/bootstrapping).

Dans le dossier *src/app*, modifiez *app.module.ts* et effectuez les modifications suivantes indiquées dans l’extrait de code ci-dessous. Les modifications sont signalées par les phrases *Changes start here* (Les modifications commencent ici) et *Changes end here* (Les modifications se terminent ici). Après les modifications, votre code doit ressembler à l’extrait de code suivant.

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';

/* Changes start here. */
// Import MSAL and MSAL browser libraries. 
import { MsalGuard, MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';
import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

// Import the Azure AD B2C configuration 
import { msalConfig, protectedResources } from './auth-config';

// Import the Angular HTTP interceptor. 
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';
import { ProfileComponent } from './profile/profile.component';
import { HomeComponent } from './home/home.component';
import { WebapiComponent } from './webapi/webapi.component';

// Add the essential Angular materials.
import { MatButtonModule } from '@angular/material/button';
import { MatToolbarModule } from '@angular/material/toolbar';
import { MatListModule } from '@angular/material/list';
import { MatTableModule } from '@angular/material/table';
/* Changes end here. */

@NgModule({
  declarations: [
    AppComponent,
    ProfileComponent,
    HomeComponent,
    WebapiComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    /* Changes start here. */
    // Import the following Angular materials. 
    MatButtonModule,
    MatToolbarModule,
    MatListModule,
    MatTableModule,
    // Import the HTTP client. 
    HttpClientModule,

    // Initiate the MSAL library with the MSAL config object
    MsalModule.forRoot(new PublicClientApplication(msalConfig),
      {
        // The routing guard configuration. 
        interactionType: InteractionType.Redirect,
        authRequest: {
          scopes: protectedResources.todoListApi.scopes
        }
      },
      {
        // MSAL interceptor configuration.
        // The protected resource mapping maps your web API with the corresponding app scopes. If your code needs to call another web API, add the URI mapping here.
        interactionType: InteractionType.Redirect,
        protectedResourceMap: new Map([
          [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
        ])
      })
    /* Changes end here. */
  ],
  providers: [
    /* Changes start here. */
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    },
    MsalGuard
    /* Changes end here. */
  ],
  bootstrap: [
    AppComponent,
    /* Changes start here. */
    MsalRedirectComponent
    /* Changes end here. */
  ]
})
export class AppModule { }
```

## <a name="configure-routes"></a>Configurer des itinéraires

Dans cette section, configurez les itinéraires de votre application Angular. Lorsqu’un utilisateur sélectionne un lien sur la page pour naviguer dans votre application monopage, ou qu’il tape une URL dans la barre d’adresses, les itinéraires mappent l’URL à un composant Angular. L’interface de routage Angular [canActivate](https://angular.io/api/router/CanActivate) utilise MSAL Guard pour vérifier si l’utilisateur est connecté. Si l’utilisateur n’est pas connecté, MSAL le dirige vers Azure AD B2C pour s’authentifier.

Dans le dossier *src/app*, modifiez *app-routing.module.ts* et effectuez les modifications suivantes indiquées dans l’extrait de code ci-dessous. Les modifications sont signalées par les phrases *Changes start here* (Les modifications commencent ici) et *Changes end here* (Les modifications se terminent ici). 

Après les modifications, votre code doit ressembler à l’extrait de code suivant.

```typescript
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';
import { ProfileComponent } from './profile/profile.component';
import { WebapiComponent } from './webapi/webapi.component';

const routes: Routes = [
  /* Changes start here. */
  {
    path: 'profile',
    component: ProfileComponent,
    // The profile component is protected with MSAL guard.
    canActivate: [MsalGuard]
  },
  {
    path: 'webapi',
    component: WebapiComponent,
    // The profile component is protected with MSAL guard.
    canActivate: [MsalGuard]
  },
  {
    // The home component allows anonymous access
    path: '',
    component: HomeComponent
  }
  /* Changes end here. */
];


@NgModule({
  /* Changes start here. */
  // Replace the following line with the next one
  //imports: [RouterModule.forRoot(routes)],
  imports: [RouterModule.forRoot(routes, {
    initialNavigation:'enabled'
  })],
  /* Changes end here. */
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

## <a name="add-the-sign-in-and-sign-out-buttons"></a>Ajouter les boutons de connexion et de déconnexion

Dans cette section, vous allez ajouter les boutons de connexion et de déconnexion au composant *app*. Dans le dossier *src/app*, ouvrez *app.component.ts* et effectuez les modifications suivantes :

1. Importez les composants requis.
1. Modifiez la classe pour implémenter la [méthode OnInit](https://angular.io/api/core/OnInit). La méthode `OnInit` s’abonne à l’événement observable [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `inProgress$`. Utilisez cet événement pour connaître l’état des interactions de l’utilisateur, en particulier pour vérifier que les interactions sont terminées. Avant d’interagir avec l’objet de compte MSAL, vérifiez que la propriété `InteractionStatus` renvoie `InteractionStatus.None`. L’événement `subscribe` appelle la méthode `setLoginDisplay` pour vérifier si l’utilisateur est authentifié.
1. Ajoutez des variables de classe.
1. Ajoutez la méthode `login` qui démarre le flux d’autorisation.
1. Ajoutez la méthode `logout` qui déconnecte l’utilisateur. 
1. Ajoutez la méthode `setLoginDisplay` qui vérifie si l’utilisateur est authentifié.
1. Ajoutez la méthode [ngOnDestroy](https://angular.io/api/core/OnDestroy) qui nettoie l’événement d’abonnement `inProgress$`.

Après les modifications, votre code doit ressembler à l’extrait de code suivant :

```typescript
import { Component, OnInit, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})

/* Changes start here. */
export class AppComponent implements OnInit{
  title = 'msal-angular-tutorial';
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { 
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
  /* Changes end here. */
}
```

Dans le dossier *src/app*, modifiez *app.component.html* et effectuez les modifications suivantes :

1. Ajoutez un lien vers le profil et les composants de l’API Web.
1. Ajoutez le bouton de connexion avec l’attribut d’événement de clic défini sur la méthode `login()`. Ce bouton n’apparaît que si la variable de classe `loginDisplay` est `false`.
1. Ajoutez le bouton de déconnexion avec l’attribut d’événement de clic défini sur la méthode `logout()`. Ce bouton n’apparaît que si la variable de classe `loginDisplay` est `true`.
1. Ajoutez un élément [router-outlet](https://angular.io/api/router/RouterOutlet). 

Après les modifications, votre code doit ressembler à l’extrait de code suivant.

```html
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>
  <a mat-button [routerLink]="['webapi']">Web API</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <router-outlet></router-outlet>
</div>
```

Si vous le souhaitez, mettez à jour le fichier *app.component.css* avec l’extrait CSS suivant. 

```css
.toolbar-spacer {
    flex: 1 1 auto;
  }

  a.title {
    color: white;
  }
```

## <a name="handle-the-app-redirects"></a>Gérer les redirections de l’application 

En cas d’utilisation de redirections avec MSAL, il est obligatoire d’ajouter la directive [app-redirect](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/redirects.md) à *index.html*. Dans le dossier *src*, modifiez *index.html*. 

Après les modifications, votre code doit ressembler à l’extrait de code suivant.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>MsalAngularTutorial</title>
  <base href="/">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="icon" type="image/x-icon" href="favicon.ico">
</head>
<body>
  <app-root></app-root>
  <!-- Changes start here -->
  <app-redirect></app-redirect>
  <!-- Changes end here -->
</body>
</html>
```

## <a name="set-app-css-optional"></a>Définir la CSS de l’application (facultatif)

Dans le dossier */src*, mettez à jour le fichier *styles.css* avec l’extrait CSS suivant. 

```css
@import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

html, body { height: 100%; }
body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
.container { margin: 1%; }
```

> [!TIP]
> À ce stade, vous pouvez exécuter votre application et tester l’expérience de connexion. Pour exécuter votre application, consultez la section [Exécuter l’application Angular](#run-the-angular-application).

## <a name="check-if-a-user-is-authenticated"></a>Vérifier si un utilisateur est authentifié

`home.component` montre comment vérifier que l’utilisateur est authentifié. Dans le dossier *src/app/home*, mettez à jour *home.component.ts* avec l’extrait de code suivant. 


Le code :

1. S’abonne aux événements observables [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `msalSubject$` et `inProgress$`. 
1. `msalSubject$` écrit le résultat de l’authentification dans la console du navigateur.
1. `inProgress$` vérifie si un utilisateur est authentifié. `getAllAccounts()` renvoie un ou plusieurs objets.


```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { filter } from 'rxjs/operators';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  loginDisplay = false;

  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {
    this.msalBroadcastService.msalSubject$
      .pipe(
        filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
      )
      .subscribe((result: EventMessage) => {
        console.log(result);
      });

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) => status === InteractionStatus.None)
      )
      .subscribe(() => {
        this.setLoginDisplay();
      })
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }
}
```

Dans le dossier *src/app/home*, mettez à jour *home.component.html* avec l’extrait HTML suivant. La directive [*ngIf](https://angular.io/api/common/NgIf) vérifie la variable de classe `loginDisplay` pour afficher ou masquer les messages de bienvenue.

```html
<div *ngIf="!loginDisplay">
    <p>Please sign-in to see your profile information.</p>
</div>

<div *ngIf="loginDisplay">
    <p>Login successful!</p>
    <p>Request your profile information by clicking Profile above.</p>
</div>
```

## <a name="read-the-id-token-claims"></a>Lire les revendications de jetons d’ID

`profile.component` montre comment accéder aux revendications de jeton d’ID de l’utilisateur. Dans le dossier *src/app/profile*, mettez à jour *profile.component.ts* avec l’extrait de code suivant. 

Le code :

1. Importe les composants requis.
1. S’abonne à l’événement observable [MSAL MsalBroadcastService](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/events.md) `inProgress$`. L’événement charge le compte et lit les revendications du jeton d’ID.
1. La méthode `checkAndSetActiveAccount` vérifie et définit le compte actif. Cela est courant lorsque l’application interagit avec plusieurs flux d’utilisateurs ou stratégies personnalisées Azure AD B2C.
1. La méthode `getClaims` obtient les revendications de jeton d’ID à partir de l’objet de compte MSAL actif. Elle les ajoute ensuite au tableau `dataSource`. Le tableau est affiché à l’utilisateur avec la liaison de modèle du composant.

```typescript
import { Component, OnInit } from '@angular/core';
import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-profile',
  templateUrl: './profile.component.html',
  styleUrls: ['./profile.component.css']
})

export class ProfileComponent implements OnInit {
  displayedColumns: string[] = ['claim', 'value'];
  dataSource: Claim[] = [];
  private readonly _destroying$ = new Subject<void>();
  
  constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

  ngOnInit(): void {

    this.msalBroadcastService.inProgress$
      .pipe(
        filter((status: InteractionStatus) =>  status === InteractionStatus.None || status === InteractionStatus.HandleRedirect),
        takeUntil(this._destroying$)
      )
      .subscribe(() => {
        this.checkAndSetActiveAccount();
        this.getClaims(this.authService.instance.getActiveAccount()?.idTokenClaims)
      })
  }

  checkAndSetActiveAccount() {

    let activeAccount = this.authService.instance.getActiveAccount();

    if (!activeAccount && this.authService.instance.getAllAccounts().length > 0) {
      let accounts = this.authService.instance.getAllAccounts();
      this.authService.instance.setActiveAccount(accounts[0]);
    }
  }

  getClaims(claims: any) {

    let list: Claim[]  =  new Array<Claim>();

    Object.keys(claims).forEach(function(k, v){
      
      let c = new Claim()
      c.id = v;
      c.claim = k;
      c.value =  claims ? claims[k]: null;
      list.push(c);
    });
    this.dataSource = list;

  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}

export class Claim {
  id: number;
  claim: string;
  value: string;
}
``` 

Dans le dossier *src/app/profile*, mettez à jour *profile.component.html* avec l’extrait HTML suivant. 

```html
<h1>ID token claims:</h1>

<table mat-table [dataSource]="dataSource" class="mat-elevation-z8">

  <!-- Claim Column -->
  <ng-container matColumnDef="claim">
    <th mat-header-cell *matHeaderCellDef> Claim </th>
    <td mat-cell *matCellDef="let element"> {{element.claim}} </td>
  </ng-container>

  <!-- Value Column -->
  <ng-container matColumnDef="value">
    <th mat-header-cell *matHeaderCellDef> Value </th>
    <td mat-cell *matCellDef="let element"> {{element.value}} </td>
  </ng-container>

  <tr mat-header-row *matHeaderRowDef="displayedColumns"></tr>
  <tr mat-row *matRowDef="let row; columns: displayedColumns;"></tr>
</table>
```

## <a name="call-a-web-api"></a>Appeler une API web

Pour appeler une [API web d’autorisation basée sur les jetons](enable-authentication-web-api.md), l’application doit disposer d’un jeton d’accès valide. Le fournisseur [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) acquiert automatiquement des jetons pour les requêtes sortantes qui utilisent [HttpClient](https://angular.io/api/common/http/HttpClient) Angular pour les ressources connues protégées.

> [!IMPORTANT]
> La méthode d’initialisation MSAL (dans la classe *app.module.ts*) mappe des ressources des protégées, comme des API web avec les étendues d’application requises à l’aide de l’objet `protectedResourceMap`. Si votre code doit appeler une autre API web, ajoutez l’URI de l’API Web, la méthode HTTP de l’API Web, avec les étendues correspondantes à l’objet `protectedResourceMap`. Pour plus d’informations, consultez [Mappages des ressources protégées](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/master/lib/msal-angular/docs/v2-docs/msal-interceptor.md#protected-resource-map).


Lorsque l’objet [HttpClient](https://angular.io/api/common/http/HttpClient) appelle une API web, le fournisseur [MsalInterceptor](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/msal-interceptor.md) accomplit les étapes suivantes :

1. Il obtient un jeton d’accès avec les autorisations requises (étendues) pour le point de terminaison de l’API web. 
1. Il transmet le jeton d’accès en tant que porteur dans l’en-tête d’autorisation de la requête HTTP en utilisant le format suivant :

```http
Authorization: Bearer <access-token>
```

`webapi.component` montre comment appeler une API web. Dans le dossier *src/app/webapi*, mettez à jour *webapi.component.ts* avec l’extrait de code suivant.  

Le code suivant :

1. Utilise [HttpClient](https://angular.io/guide/http) Angular pour appeler l’API web.
1. Lit l’élément `protectedResources.todoListApi.endpoint` de la classe `auth-config`. Cet élément spécifie l’URI de l’API web. D’après l’URI de l’API web, l’intercepteur MSAL acquiert un jeton d’accès avec les étendues correspondantes. 
1. Obtient le profil de l’API web et définit la variable de classe `profile`.

```typescript
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { protectedResources } from '../auth-config';

type ProfileType = {
  name?: string
};

@Component({
  selector: 'app-webapi',
  templateUrl: './webapi.component.html',
  styleUrls: ['./webapi.component.css']
})
export class WebapiComponent implements OnInit {
  todoListEndpoint: string = protectedResources.todoListApi.endpoint;
  profile!: ProfileType;

  constructor(
    private http: HttpClient
  ) { }

  ngOnInit() {
    this.getProfile();
  }

  getProfile() {
    this.http.get(this.todoListEndpoint)
      .subscribe(profile => {
        this.profile = profile;
      });
  }
}
```

Dans le dossier *src/app/webapi*, mettez à jour *webapi.component.html* avec l’extrait HTML suivant. Le modèle du composant affiche le `name` qui est renvoyé par l’API web. En bas de la page, le modèle affiche l’adresse de l’API web.

```html
<h1>The web API returns:</h1>
<div>
    <p><strong>Name: </strong> {{profile?.name}}</p>
</div>

<div class="footer-text">
    Web API: {{todoListEndpoint}}
</div>
```

Si vous le souhaitez, mettez à jour le fichier *webapi.component.css* avec l’extrait CSS suivant. 

```css
.footer-text {
    position: absolute;
    bottom: 50px;
    color: gray;
}
```

## <a name="run-the-angular-application"></a>Exécuter l’application Angular


Exécutez les commandes suivantes :

```console
npm start
```

La fenêtre de console affiche le numéro de port de l’endroit où est hébergée l’application.

```console
Listening on port 4200...
```

> [!TIP]
> Au lieu d’exécuter la commande `npm start`, vous pouvez utiliser le [débogueur VS Code](https://code.visualstudio.com/docs/editor/debugging). Le débogueur intégré de VS Code permet d’accélérer votre boucle de modification, compilation et débogage.

Accédez à `http://localhost:4200` dans votre navigateur pour voir l’application.


## <a name="next-steps"></a>Étapes suivantes

* Configurez les [options d’authentification dans votre propre application Angular à l’aide d’Azure AD B2C](enable-authentication-angular-spa-app-options.md)
* [Activez l’authentification dans votre propre API web](enable-authentication-web-api.md)
