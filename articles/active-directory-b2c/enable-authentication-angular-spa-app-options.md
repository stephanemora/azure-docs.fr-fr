---
title: Activer les options d’application Angular à l’aide d’Azure Active Directory B2C
description: Activez l’utilisation des options d’application Angular de plusieurs façons.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 94057e0b1d566d092089725dea96a53e907f2a71
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041098"
---
# <a name="configure-authentication-options-in-an-angular-application-by-using-azure-active-directory-b2c"></a>Configurer les options d’authentification dans une application Angular à l’aide d’Azure Active Directory B2C

Cet article décrit les méthodes permettant de personnaliser et d’améliorer l’expérience d’authentification Azure Active Directory B2C (Azure AD B2C) pour votre application monopage (SPA) Angular. Avant de commencer, consultez l’article [Configurer l’authentification dans une application monopage (SPA) Angular](configure-authentication-sample-angular-spa-app.md) ou [Activer l’authentification dans votre propre application monopage (SPA) Angular](enable-authentication-angular-spa-app.md).


## <a name="sign-in-and-sign-out-behavior"></a>Comportement de connexion et de déconnexion


Vous pouvez configurer votre application monopage pour la connexion des utilisateurs avec MSAL.js de deux façons :

- **Fenêtre contextuelle** : l’authentification se déroule dans une fenêtre contextuelle, et l’état de l’application est conservé. Optez pour cette approche si vous souhaitez que les utilisateurs restent sur la page de votre application pendant l’authentification.  Notez qu’il existe des [problèmes connus liés à l’utilisation de fenêtres contextuelles sur Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups).
  - Pour la connexion avec des fenêtres contextuelles, dans la classe `src/app/app.component.ts`, utilisez la méthode `loginPopup`.  
  - Dans la classe `src/app/app.module.ts`, définissez l’attribut `interactionType` sur `InteractionType.Popup`.
  - Pour la déconnexion avec des fenêtres contextuelles, dans la classe `src/app/app.component.ts`, utilisez la méthode `logoutPopup`. Vous pouvez également configurer `logoutPopup` de manière à rediriger la fenêtre principale vers une autre page, telle que la page d’accueil ou la page de connexion, une fois la déconnexion terminée en transmettant `mainWindowRedirectUri` dans le cadre de la requête.
- **Redirection** : l’utilisateur est redirigé vers Azure AD B2C pour mettre fin au flux d’authentification. Optez pour cette approche si les utilisateurs sont soumis à des contraintes imposées par le navigateur ou par des stratégies qui bloquent les fenêtres contextuelles. 
  - Pour la connexion avec redirection, dans la classe `src/app/app.component.ts`, utilisez la méthode `loginRedirect`.  
  - Dans la classe `src/app/app.module.ts`, définissez l’attribut `interactionType` sur `InteractionType.Redirect`.
  - Pour la déconnexion avec redirection, dans la classe `src/app/app.component.ts`, utilisez la méthode `logoutRedirect`. Pour configurer l’URI de redirection après la déconnexion, définissez `postLogoutRedirectUri`. Cet URI doit être enregistré en tant qu’URI de redirection dans l’inscription de votre application.
  
L’exemple suivant montre comment se connecter et se déconnecter :

#### <a name="pop-up"></a>[Fenêtre contextuelle](#tab/popup)


```typescript
//src/app/app.component.ts
login() {
  if (this.msalGuardConfig.authRequest){
    this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest);
  } else {
    this.authService.loginPopup();
  }
}

logout() { 
  this.authService.logoutPopup({
    mainWindowRedirectUri: '/',
  });
}
```

#### <a name="redirect"></a>[Rediriger](#tab/redirect)

```typescript
//src/app/app.component.ts
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
```

---

La bibliothèque MSAL Angular contient trois flux de connexion : connexion interactive (où un utilisateur sélectionne le bouton de connexion), MSAL Guard et MSAL Interceptor. Les configurations de MSAL Guard et MSAL Interceptor s’appliquent lorsqu’un utilisateur tente d’accéder à une ressource protégée sans jeton d’accès valide. Dans ce cas, la bibliothèque MSAL force l’utilisateur à se connecter. 

Les exemples suivants montrent comment configurer MSAL Guard et MSAL Interceptor pour la connexion à l’aide d’une fenêtre contextuelle ou d’une redirection : 

#### <a name="pop-up"></a>[Fenêtre contextuelle](#tab/popup)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Popup,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

#### <a name="redirect"></a>[Rediriger](#tab/redirect)

```typescript
// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
    }
  },
  {
    interactionType: InteractionType.Redirect,
    protectedResourceMap: new Map([
      [protectedResources.todoListApi.endpoint, protectedResources.todoListApi.scopes]
    ])
  })
```

---  

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si vous utilisez une stratégie personnalisée, ajoutez la revendication d’entrée requise comme décrit dans l’article [Configurer la connexion directe](direct-signin.md#prepopulate-the-sign-in-name). 
1. Créez ou utilisez un objet de configuration MSAL `PopupRequest` ou `RedirectRequest` existant.
1. Définissez l’attribut `loginHint` avec l’indicateur de connexion correspondant. 

Les extraits de code suivants montrent comment passer le paramètre d’indicateur de connexion. Ils utilisent `bob@contoso.com` comme valeur d’attribut.

#### <a name="pop-up"></a>[Fenêtre contextuelle](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

#### <a name="redirect"></a>[Rediriger](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.loginHint = "bob@contoso.com"

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      loginHint: "bob@contoso.com"
    }
  },
```

---  


[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Vérifiez le nom de domaine de votre fournisseur d’identité externe. Pour plus d’informations, consultez [Rediriger la connexion vers un fournisseur social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Créez ou utilisez un objet de configuration MSAL `PopupRequest` ou `RedirectRequest` existant.
1. Définissez l’attribut `domainHint` avec l’indicateur de domaine correspondant.

Les extraits de code suivants montrent comment passer le paramètre d’indicateur de domaine. Ils utilisent `facebook.com` comme valeur d’attribut.

#### <a name="pop-up"></a>[Fenêtre contextuelle](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

#### <a name="redirect"></a>[Rediriger](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.domainHint = "facebook.com";

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      domainHint: "facebook.com"
    }
  },
```

---  

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configurez la personnalisation de la langue](language-customization.md). 
1. Créez ou utilisez un objet de configuration MSAL `PopupRequest` ou `RedirectRequest` existant avec des attributs `extraQueryParameters`.
1. Ajoutez à l’attribut `extraQueryParameters` le paramètre `ui_locales` avec le code de langue correspondant.

Les extraits de code suivants montrent comment passer le paramètre d’indicateur de domaine. Ils utilisent `es-es` comme valeur d’attribut.

#### <a name="pop-up"></a>[Fenêtre contextuelle](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[Rediriger](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"ui_locales" : "es-es"};

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

---   
 

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configurez l’élément [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Créez ou utilisez un objet de configuration MSAL `PopupRequest` ou `RedirectRequest` existant avec des attributs `extraQueryParameters`.
1. Ajoutez le paramètre de chaîne de requête personnalisé, par exemple `campaignId`. Définissez la valeur du paramètre. 

Les extraits de code suivants montrent comment passer un paramètre de chaîne de requête personnalisé. Ils utilisent `germany-promotion` comme valeur d’attribut.

#### <a name="pop-up"></a>[Fenêtre contextuelle](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"ui_locales" : "es-es"}
    }
  },
```

#### <a name="redirect"></a>[Rediriger](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"campaignId": 'germany-promotion'}

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"campaignId" : "germany-promotion"}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Dans votre stratégie personnalisée, définissez le [profil technique d’un indicateur de jeton d’ID](id-token-hint.md).
1. Créez ou utilisez un objet de configuration MSAL `PopupRequest` ou `RedirectRequest` existant avec des attributs `extraQueryParameters`.
1. Ajoutez le paramètre `id_token_hint` avec la variable correspondante qui stocke le jeton d’ID.

Les extraits de code suivants montrent comment définir un indicateur de jeton d’ID :

#### <a name="pop-up"></a>[Fenêtre contextuelle](#tab/popup)

```typescript
// src/app/app.component.ts
let authRequestConfig: PopupRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as PopupRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};

this.authService.loginPopup(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Popup,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

#### <a name="redirect"></a>[Rediriger](#tab/redirect)

```typescript
// src/app/app.component.ts
let authRequestConfig: RedirectRequest;

if (this.msalGuardConfig.authRequest) {
  authRequestConfig = { ...this.msalGuardConfig.authRequest } as RedirectRequest
}

authRequestConfig.extraQueryParameters = {"id_token_hint": idToken};;

this.authService.loginRedirect(authRequestConfig);

// src/app/app.module.ts
MsalModule.forRoot(new PublicClientApplication(msalConfig),
  {
    interactionType: InteractionType.Redirect,
    authRequest: {
      scopes: protectedResources.todoListApi.scopes,
      extraQueryParameters: {"id_token_hint" : idToken}
    }
  },
```

---

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Pour utiliser votre domaine personnalisé en tant qu’ID de locataire dans l’URL d’authentification, suivez les instructions fournies dans [Activer des domaines personnalisés](custom-domain.md). Ouvrez l’objet de configuration MSAL `knownAuthorities`, puis modifiez les valeurs `src/app/auth-config.ts` et `authorities` de façon à utiliser vos nom de domaine et ID de locataire personnalisés.  

Le code JavaScript suivant montre l’objet de configuration MSAL avant changement : 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_susi",
      knownAuthorities: ["fabrikamb2c.b2clogin.com"],
      ...
    },
  ...
}
```  

Le code JavaScript suivant montre l’objet de configuration MSAL après changement : 

```typescript
const msalConfig = {
    auth: {
      ...
      authority: "https://custom.domain.com/00000000-0000-0000-0000-000000000000/B2C_1_susi",
      knownAuthorities: ["custom.domain.com"],
      ...
    },
  ...
}
```  


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]

Pour configurer la [journalisation](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/docs/v2-docs/logging.md) Angular, dans le fichier *src/app/auth-config.ts*, configurez les clés suivantes :

- `loggerCallback` est la fonction de rappel d’enregistreur d’événements. 
- `logLevel` vous permet de spécifier le niveau de journalisation. Valeurs possibles : `Error`, `Warning`, `Info` et `Verbose`.
- `piiLoggingEnabled` active l’entrée de données personnelles. Valeurs possibles : `true` ou `false`.
 
L’extrait de code suivant montre comment configurer la journalisation MSAL :

```typescript
export const msalConfig: Configuration = {
  ...
  system: {
    loggerOptions: {
        loggerCallback: (logLevel, message, containsPii) => {  
            console.log(message);
          },
          logLevel: LogLevel.Verbose,
          piiLoggingEnabled: false
      }
  }
  ...
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir plus, consultez [Options de configuration de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md).
