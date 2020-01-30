---
title: Initialiser des applications clientes MSAL.js | Azure
titleSuffix: Microsoft identity platform
description: En savoir plus sur l’initialisation d’applications clientes à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e350f4fc3d40b45a1308e1edd9331dc7f71399c5
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696127"
---
# <a name="initialize-client-applications-using-msaljs"></a>Initialiser des applications clientes avec MSAL.js
Cet article décrit l’initialisation de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) avec une instance d’application agent utilisateur. L’application agent utilisateur est une forme d'application cliente publique dans laquelle le code client est exécuté dans un agent utilisateur, par exemple un navigateur web. Ces clients ne stockent pas de secrets car le contexte du navigateur est directement accessible. Pour en savoir plus sur les types d’applications clientes et les options de configuration d’application, consultez la [vue d’ensemble](msal-client-applications.md).

## <a name="prerequisites"></a>Conditions préalables requises
Avant d’initialiser une application, vous devez d’abord [l’inscrire sur le portail Azure](scenario-spa-app-registration.md) afin que votre application puisse être intégrée à la plate-forme d’identité Microsoft. Après l’inscription, vous aurez peut-être besoin des informations suivantes (que vous trouverez sur le Portail Azure) :

- L’ID client (une chaîne représentant un GUID pour votre application)
- L’URL du fournisseur d’identité (l’instance) et l’audience de connexion pour votre application. Ces deux paramètres représentent collectivement l’autorité.
- L’ID locataire, si vous écrivez une application cœur de métier uniquement pour votre organisation (également nommée application à locataire unique).
- Pour les applications web, vous devrez également définir l’URI de redirection (redirectUri) utilisé par le fournisseur d’identité pour retourner à votre application avec les jetons de sécurité.

## <a name="initializing-applications"></a>Initialisation d’applications

Vous pouvez utiliser MSAL.js comme suit dans une application JavaScript/Typescript brute. Initialisez le contexte d’authentification MSAL en instanciant `UserAgentApplication` avec un objet de configuration. La configuration minimale requise pour initialiser MSAL.js est l’ID client de votre application que vous devez obtenir à partir du portail d’inscription de l’application.

Pour les méthodes d’authentification avec flux de redirection (`loginRedirect` et `acquireTokenRedirect`), vous devez inscrire explicitement un rappel de réussite ou d’erreur via la méthode `handleRedirectCallback()`. Cette opération est nécessaire car les flux de redirection ne retournent pas de promesses comme le font les méthodes proposant une expérience contextuelle.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL.js est conçu pour disposer d’une seule instance et configuration de `UserAgentApplication` pour représenter un contexte d’authentification unique. Il est déconseillé d’utiliser plusieurs instances car elles entraînent des conflits au niveau des entrées du cache et du comportement dans le navigateur.

## <a name="configuration-options"></a>Options de configuration

MSAL.js possède un objet de configuration indiqué ci-dessous, qui fournit un regroupement d’options configurables pour créer une instance de `UserAgentApplication`.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

Voici l’ensemble des options configurables actuellement prises en charge dans l’objet de configuration :

- **clientID** : Obligatoire. L’ID client de votre application que vous devez obtenir à partir du portail d’inscription de l’application.

- **authority** : facultatif. URL indiquant un répertoire à partir duquel MSAL peut demander des jetons. La valeur par défaut est `https://login.microsoftonline.com/common`.
    * Dans Azure AD, son format est https://&lt;instance&gt;/&lt;audience&gt;, où &lt;instance&gt; est le domaine du fournisseur d'identité (par exemple, `https://login.microsoftonline.com`) et &lt;audience&gt; est un identificateur représentant l’audience de connexion. Les valeurs suivantes peuvent être définies :
        * `https://login.microsoftonline.com/<tenant>`- tenant est un domaine associé au locataire, par exemple contoso.onmicrosoft.com, ou le GUID représentant la propriété `TenantID` du répertoire utilisé uniquement pour connecter les utilisateurs d’une organisation spécifique.
        * `https://login.microsoftonline.com/common`- permet de connecter des utilisateurs disposant d’un compte professionnel et scolaire, ou d’un compte Microsoft personnel.
        * `https://login.microsoftonline.com/organizations/`- permet de connecter des utilisateurs disposant d’un compte professionnel et scolaire.
        * `https://login.microsoftonline.com/consumers/`- permet de connecter des utilisateurs disposant uniquement d’un compte Microsoft personnel (live).
    * Dans Azure AD B2C, le format est `https://<instance>/tfp/<tenant>/<policyName>/`, où l’instance est le domaine Azure AD B2C, c’est-à-dire {your-tenant-name}.b2clogin.com, le locataire est le nom du locataire Azure AD B2C, c’est-à-dire {your-tenant-name}.onmicrosoft.com et policyName est le nom de la stratégie B2C à appliquer.


- **validateAuthority** : facultatif.  Validez l’émetteur des jetons. La valeur par défaut est `true`. Pour les applications B2C, étant donné que la valeur d’autorité est connue et peut être différente pour chaque stratégie, la validation de l’autorité ne fonctionne pas et doit être définie sur `false`.

- **redirectUri** : facultatif.  L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail. La valeur par défaut est `window.location.href`.

- **postLogoutRedirectUri** : facultatif.  Redirige l’utilisateur vers `postLogoutRedirectUri` après la déconnexion. Par défaut, il s’agit de `redirectUri`.

- **navigateToLoginRequestUrl** : facultatif. Possibilité de désactiver la navigation par défaut vers la page de démarrage après la connexion. La valeur par défaut est true. S’applique uniquement aux flux de redirection.

- **cacheLocation** : facultatif.  Définit le stockage du navigateur sur `localStorage` ou `sessionStorage`. Par défaut, il s’agit de `sessionStorage`.

- **storeAuthStateInCookie** : facultatif.  Cet indicateur a été introduit dans MSAL.js v0.2.2 comme un correctif aux [problèmes d’authentification en boucle](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) sur Microsoft Internet Explorer et Microsoft Edge. Définissez l’indicateur `storeAuthStateInCookie` sur true (vrai) pour tirer parti de ce correctif. Lorsque cette option est désactivée, MSAL.js stocke l’état de la demande d’authentification nécessaire pour la validation des flux d’authentification dans les cookies de navigateur. Par défaut, cet indicateur est défini sur `false`.

- **logger** : facultatif.  un objet d’enregistreur d'événements avec une instance de rappel qui peut être fournie par le développeur pour consommer et publier des journaux de manière personnalisée. Pour plus d’informations sur le passage d’un objet enregistreur d’événements, consultez [Journalisation avec msal.js](msal-logging.md).

- **loadFrameTimeout** : facultatif.  Le nombre de millisecondes d’inactivité avant qu’une réponse de renouvellement de jeton provenant d’Azure AD doit être considérée comme expirée. La valeur par défaut est 6 secondes.

- **tokenRenewalOffsetSeconds** : facultatif. Le nombre de millisecondes qui définit la fenêtre de décalage nécessaire pour renouveler le jeton avant son expiration. La valeur par défaut est de 300 millisecondes.

- **navigateFrameWait** : facultatif. Nombre de millisecondes définissant le délai d’attente avant que les iframes masqués accèdent à leur destination. La valeur par défaut est de 500 millisecondes.

Ces valeurs doivent uniquement être transmises à partir de la bibliothèque wrapper MSAL Angular :
- **unprotectedResources** : facultatif.  Tableau des URI représentant des ressources non protégées. MSAL ne joindra pas de jeton aux demandes sortantes affichant ces URI. La valeur par défaut est `null`.

- **protectedResourceMap** : facultatif.  Mappage des ressources aux étendues utilisées par MSAL pour joindre automatiquement des jetons d’accès dans les appels d’API web. Un seul jeton d’accès est obtenu pour la ressource. Vous pouvez donc mapper un chemin d’accès à une ressource spécifique comme suit : {"https://graph.microsoft.com/v1.0/me", ["user.read"]}, ou l’URL de l’app de la ressource : {"https://graph.microsoft.com/", ["user.read", "mail.send"]}. Cela est nécessaire pour les appels CORS. La valeur par défaut est `null`.
