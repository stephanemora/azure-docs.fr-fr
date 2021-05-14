---
title: Acquérir un jeton pour appeler une API web (applications monopages) | Azure
titleSuffix: Microsoft identity platform
description: Découvrir comment créer une application monopage (acquérir un jeton pour appeler une API)
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/2/2021
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 2e4369c729fc5497f615f64c1f7235d19c293f98
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227737"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Application monopage : Acquérir un jeton pour appeler une API

Le modèle permettant d'acquérir des jetons pour les API avec [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js) consiste d'abord à tenter une demande de jeton en mode silencieux à l'aide de la méthode `acquireTokenSilent`. Lorsque cette méthode est appelée, la bibliothèque vérifie d’abord le cache dans le stockage du navigateur pour voir s’il existe un jeton valide, et le retourne. En l'absence de jeton valide dans le cache, elle tente d'utiliser son jeton d'actualisation pour obtenir le jeton. Si la durée de vie de 24 heures du jeton d'actualisation a expiré, MSAL.js ouvre un iframe masqué pour demander en mode silencieux un nouveau code d'autorisation, qu'il échangera contre un nouveau jeton d'actualisation valide. Pour plus d’informations sur la session d’authentification unique et les valeurs de durée de vie des jetons dans Azure AD, consultez [Durée de vie des jetons](active-directory-configurable-token-lifetimes.md).

Les demandes de jeton en mode silencieux adressées à Azure AD peuvent échouer pour des raisons telles qu'un changement de mot de passe ou la mise à jour des stratégies d'accès conditionnel.  Le plus souvent, les échecs sont dus à l'expiration de la durée de vie de 24 heures du jeton d'actualisation et au [blocage des cookies de tiers par le navigateur](reference-third-party-cookies-spas.md), ce qui empêche l'utilisation des iframes masqués pour poursuivre l'authentification de l'utilisateur.  Dans ces cas, vous devez appeler l'une des méthodes interactives (qui peut envoyer une invite à l'utilisateur) pour acquérir des jetons :

* [Fenêtre contextuelle](#acquire-a-token-with-a-pop-up-window), au moyen de `acquireTokenPopup`
* [Redirection](#acquire-a-token-with-a-redirect), au moyen de `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Choisir entre une expérience avec fenêtre contextuelle ou redirection

Le choix entre la fenêtre contextuelle et la redirection dépend du flux de votre application :

* Si vous ne souhaitez pas que les utilisateurs quittent la page principale de votre application lors de l’authentification, nous vous recommandons d’utiliser la méthode avec fenêtre contextuelle. La redirection de l’authentification se produisant dans une fenêtre contextuelle, l’état de l’application principale est conservé.

* Si les utilisateurs sont soumis à des contraintes imposées par le navigateur ou par des stratégies qui bloquent les fenêtres contextuelles, vous pouvez utiliser la méthode avec redirection. Utilisez la méthode avec redirection dans le navigateur Internet Explorer, car il existe des [problèmes connus avec l’utilisation de fenêtres contextuelles dans Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups).

Vous pouvez définir les étendues d’API qui doivent être incluses dans le jeton d’accès lors de la création de la requête de jeton d’accès. Notez que les étendues demandées sont susceptibles de ne pas être toutes accordées dans le jeton d’accès. Cela dépend du consentement de l’utilisateur.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Acquérir un jeton avec une fenêtre contextuelle

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

Le code suivant combine le modèle décrit précédemment aux méthodes offrant une expérience de fenêtre contextuelle :

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error instanceof InteractionRequiredAuthError) {
        publicClientApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
            let accessToken = accessTokenResponse.accessToken;
            // Call your API with token
            callApi(accessToken);
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

Le code suivant combine le modèle décrit précédemment aux méthodes offrant une expérience de fenêtre contextuelle :

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
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

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

Le wrapper MSAL Angular fournit l’intercepteur HTTP, qui acquiert automatiquement des jetons d’accès en mode silencieux et les joint aux requêtes HTTP destinées aux API.

Vous pouvez spécifier les étendues des API dans l’option de configuration `protectedResourceMap`. `MsalInterceptor` demande ces étendues lors de l’acquisition automatique de jetons.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule } from '@azure/msal-angular';

@NgModule({
    declarations: [
        // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
        auth: {
            clientId: 'Enter_the_Application_Id_Here',
        },
        cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
        }
        }), {
            interactionType: InteractionType.Popup,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Popup,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
        })
    ],
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

Pour la réussite et l'échec de l'acquisition de jetons en mode silencieux, MSAL Angular fournit des événements auxquels vous pouvez vous abonner. Il est également important de se souvenir d’annuler l’abonnement.

```javascript
import { MsalBroadcastService } from '@azure/msal-angular';
import { EventMessage, EventType } from '@azure/msal-browser';

// In app.component.ts
export class AppComponent implements OnInit {
    private readonly _destroying$ = new Subject<void>();

    constructor(private broadcastService: MsalBroadcastService) { }

    ngOnInit() {
        this.broadcastService.msalSubject$
        .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.ACQUIRE_TOKEN_SUCCESS),
            takeUntil(this._destroying$)
        )
        .subscribe((result: EventMessage) => {
            // Do something with event payload here
        });
    }

    ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
    }
}
```

Sinon, vous pouvez explicitement acquérir des jetons à l’aide des méthodes d’acquisition de jeton, comme décrit dans la bibliothèque MSAL.js principale.

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
Le wrapper MSAL Angular fournit l’intercepteur HTTP, qui acquiert automatiquement des jetons d’accès en mode silencieux et les joint aux requêtes HTTP destinées aux API.
Vous pouvez spécifier les étendues des API dans l’option de configuration `protectedResourceMap`. `MsalInterceptor` demande ces étendues lors de l’acquisition automatique de jetons.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Pour la réussite et l’échec de l’acquisition de jetons en mode silencieux, MSAL Angular fournit des rappels auxquels vous pouvez vous abonner. Il est également important de se souvenir d’annuler l’abonnement.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}
ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Sinon, vous pouvez explicitement acquérir des jetons à l’aide des méthodes d’acquisition de jeton, comme décrit dans la bibliothèque MSAL.js principale.

# <a name="react"></a>[React](#tab/react)

Le code suivant combine le modèle décrit précédemment aux méthodes offrant une expérience de fenêtre contextuelle :

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        if (!apiData && inProgress === InteractionStatus.None) {
            const accessTokenRequest = {
                scopes: ["user.read"],
                account: accounts[0]
            }
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
                        // Acquire token interactive success
                        let accessToken = accessTokenResponse.accessToken;
                        // Call your API with token
                        callApi(accessToken).then((response) => { setApiData(response) });
                    }).catch(function(error) {
                        // Acquire token interactive failure
                        console.log(error);
                    });
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

Par ailleurs, si vous devez acquérir un jeton en dehors d'un composant React, vous pouvez appeler `acquireTokenSilent` mais vous ne devez pas revenir à l'interaction en cas d'échec. Toutes les interactions doivent avoir lieu sous le composant `MsalProvider` dans l’arborescence des composants.

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="acquire-a-token-with-a-redirect"></a>Acquérir un jeton avec une redirection

# <a name="javascript-msaljs-v2"></a>[JavaScript (MSAL.js v2)](#tab/javascript2)

Le modèle suivant est tel qu’il a été décrit précédemment, mais il est présenté avec une méthode de redirection qui permet d’acquérir les jetons de manière interactive. Vous devez appeler et attendre `handleRedirectPromise` lors du chargement de la page.

```javascript
const redirectResponse = await publicClientApplication.handleRedirectPromise();
if (redirectResponse !== null) {
    // Acquire token silent success
    let accessToken = redirectResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
} else {
    // MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
    const account = publicClientApplication.getAllAccounts()[0];
    
    const accessTokenRequest = {
        scopes: ["user.read"],
        account: account
    }
    
    publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
        // Acquire token silent success
        // Call API with token
        let accessToken = accessTokenResponse.accessToken;
        // Call your API with token
        callApi(accessToken);
    }).catch(function (error) {
        //Acquire token silent failure, and send an interactive request
        console.log(error);
        if (error instanceof InteractionRequiredAuthError) {
            publicClientApplication.acquireTokenRedirect(accessTokenRequest);
        }
    });
}
```

# <a name="javascript-msaljs-v1"></a>[JavaScript (MSAL.js v1)](#tab/javascript1)

Le modèle suivant est tel qu’il a été décrit précédemment, mais il est présenté avec une méthode de redirection qui permet d’acquérir les jetons de manière interactive. Vous devez inscrire le rappel de redirection, comme indiqué plus haut.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Demande de revendications facultatives

Vous pouvez utiliser des revendications facultatives aux fins suivantes :

- Inclure des revendications supplémentaires dans les jetons pour votre application.
- Modifier le comportement de certaines revendications retournées par Azure AD dans les jetons.
- Ajouter et accéder à des revendications personnalisées pour votre application.

Pour demander des revendications facultatives dans `IdToken`, vous pouvez envoyer un objet revendications stringifié au champ `claimsRequest` de la classe `AuthenticationParameters.ts`.

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

Pour en savoir plus, consultez [Revendications facultatives](active-directory-optional-claims.md).

# <a name="angular-msaljs-v2"></a>[Angular (MSAL.js v2)](#tab/angular2)

Ce code est le même que celui décrit précédemment, sauf que nous vous recommandons de démarrer le composant `MsalRedirectComponent` pour gérer les redirections. Les configurations `MsalInterceptor` peuvent également être modifiées pour utiliser des redirections.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    declarations: [
      // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Redirect,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
        })
    ],
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular (MSAL.js v1)](#tab/angular1)
Ce code est identique à celui qui est décrit plus haut.

# <a name="react"></a>[React](#tab/react)

En cas d'échec d'`acquireTokenSilent`, revenez à `acquireTokenRedirect`. Cette méthode lance une redirection complète et la réponse est gérée lors du retour à l'application. Lors de l'affichage de ce composant après le retour de la redirection, `acquireTokenSilent` doit être correctement exécuté, car les jetons seront extraits du cache.

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        const accessTokenRequest = {
            scopes: ["user.read"],
            account: accounts[0]
        }
        if (!apiData && inProgress === InteractionStatus.None) {
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenRedirect(accessTokenRequest);
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

Par ailleurs, si vous devez acquérir un jeton en dehors d'un composant React, vous pouvez appeler `acquireTokenSilent` mais vous ne devez pas revenir à l'interaction en cas d'échec. Toutes les interactions doivent avoir lieu sous le composant `MsalProvider` dans l’arborescence des composants.

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Appeler une API web](scenario-spa-call-api.md).
