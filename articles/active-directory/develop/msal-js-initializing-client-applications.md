---
title: Initialiser des applications clientes (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: En savoir plus sur l’initialisation d’applications clientes à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7990566ca9cd93e79b8356cfd15fda03a7469695
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138298"
---
# <a name="initialize-client-applications-using-msaljs"></a>Initialiser des applications clientes utilisant MSAL.js
Cet article décrit l’initialisation de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) avec une instance d’une application de l’agent utilisateur. L’application de l’agent utilisateur est un formulaire d’application de client public dans lequel le code client est exécuté dans un agent utilisateur comme un navigateur web. Ces clients ne stockent pas de secrets, étant donné que le contexte de navigateur est directement accessible. Pour en savoir plus sur les types d’applications clientes et les options de configuration d’application, consultez le [vue d’ensemble](msal-client-applications.md).

## <a name="prerequisites"></a>Conditions préalables
Avant d’initialiser une application, vous devez d’abord [Inscrivez-le avec le portail Azure](scenario-spa-app-registration.md) afin que votre application peut être intégrée à la plateforme Microsoft identity. Après l’inscription, vous devrez peut-être les informations suivantes (qui se trouve dans le portail Azure) :

- L’ID de client (une chaîne représentant un GUID pour votre application)
- L’URL de fournisseur d’identité (l’instance nommée) et l’audience de connexion pour votre application. Ces deux paramètres sont collectivement regroupés sous l’autorité.
- ID de locataire si vous écrivez une application line of business uniquement pour votre organisation (également nommée application à locataire unique).
- Pour les applications web, vous devrez également définir l’URI de redirection où le fournisseur d’identité retourne à votre application avec les jetons de sécurité.

## <a name="initializing-applications"></a>L’initialisation d’applications

Vous pouvez utiliser MSAL.js comme suit dans une application JavaScript/Typescript brute. Initialiser le contexte d’authentification MSAL en instanciant `UserAgentApplication` avec un objet de configuration. La configuration minimale requise pour initialiser MSAL.js est l’ID de client de votre application que vous devez obtenir à partir du portail de l’inscription d’application.

Pour les méthodes d’authentification avec redirige les flux (`loginRedirect` et `acquireTokenRedirect`), vous devez inscrire explicitement un rappel de réussite ou d’erreur via `handleRedirectCallback()` (méthode). Cela est nécessaire dans la mesure où les flux de redirection ne retournent pas de promesses comme les méthodes avec une expérience contextuelle.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
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

MSAL.js est conçu pour disposer d’une instance unique et la configuration de la `UserAgentApplication` pour représenter un contexte d’authentification unique. Plusieurs instances ne sont pas recommandées car elles entraînent des entrées du cache en conflit et comportement dans le navigateur.

## <a name="configuration-options"></a>Options de configuration

MSAL.js possède une configuration objet ci-dessous qui fournit un regroupement des options configurables disponibles pour la création d’une instance de `UserAgentApplication`.

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

Voici l’ensemble des options configurables qui sont actuellement pris en charge dans l’objet de configuration :

- **clientID**: Requis. ClientID de votre application, vous devez l’obtenir à partir du portail de l’inscription d’application.

- **Autorité**: facultatif. Une URL qui indique un répertoire MSAL peut demander des jetons à partir de. La valeur par défaut est `https://login.microsoftonline.com/common`.
    * Dans Azure AD, il est au format https://&lt;instance&gt;/&lt;public&gt;, où &lt;instance&gt; est le domaine de fournisseur d’identité (par exemple, `https://login.microsoftonline.com`) et &lt;public&gt; est un identificateur représentant l’audience de connexion. Cela peut être les valeurs suivantes :
        * `https://login.microsoftonline.com/<tenant>`-client est un domaine associé au locataire, par exemple contoso.onmicrosoft.com, ou le GUID représentant le `TenantID` propriété du répertoire utilisé uniquement pour connecter des utilisateurs d’une organisation spécifique.
        * `https://login.microsoftonline.com/common`-Utilisé pour connecter des utilisateurs avec le travail et les comptes scolaires ou un compte personnel Microsoft.
        * `https://login.microsoftonline.com/organizations/`-Utilisé pour connecter les utilisateurs avec des comptes professionnels et scolaires.
        * `https://login.microsoftonline.com/consumers/` -Utilisé pour connecter des utilisateurs avec leur compte Microsoft personnel uniquement (actif).
    * Dans Azure AD B2C, il est au format `https://<instance>/tfp/<tenant>/<policyName>/`, où instance est le domaine Azure AD B2C, client est le nom du locataire Azure AD B2C, policyName est le nom de la stratégie B2C à appliquer.


- **validateAuthority**: facultatif.  Valider l’émetteur de jetons. La valeur par défaut est `true`. Pour les applications B2C, étant donné que la valeur d’autorité est connue et peut être différente pour chaque stratégie, la validation de l’autorité ne fonctionne pas et doit être définie sur `false`.

- **redirectUri**: facultatif.  L’URI de redirection de votre application, vers lequel votre application peut envoyer et recevoir des réponses d’authentification. Il doit correspondre exactement à l’un des URI de redirection enregistrés dans le portail, auquel s’ajoute le codage dans une URL. La valeur par défaut est `window.location.href`.

- **postLogoutRedirectUri**: facultatif.  Redirige l’utilisateur vers `postLogoutRedirectUri` après déconnexion. Par défaut, il s’agit de `redirectUri`.

- **navigateToLoginRequestUrl**: facultatif. Possibilité de désactiver la navigation par défaut à la page de démarrage une fois la connexion. La valeur par défaut est true. Cela est utilisé uniquement pour les flux de redirection.

- **cacheLocation**: facultatif.  Définit le stockage de navigateur soit `localStorage` ou `sessionStorage`. Par défaut, il s’agit de `sessionStorage`.

- **storeAuthStateInCookie**: facultatif.  Cet indicateur a été introduit dans MSAL.js v0.2.2 comme un correctif pour le [problèmes d’authentification boucle](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) sur Microsoft Internet Explorer et Microsoft Edge. Activer l’indicateur `storeAuthStateInCookie` à vrai pour tirer parti de cette correction. Lorsque cette option est activée, MSAL.js stocke l’état de demande d’authentification requise pour la validation des flux d’authentification dans les cookies de navigateur. Par défaut, cet indicateur est défini `false`.

- **Enregistreur d’événements**: facultatif.  Un objet enregistreur d’événements avec une instance de rappel qui peut être fourni par le développeur de consommer et publier des journaux de manière personnalisée. Pour plus d’informations sur la transmission d’objet enregistreur d’événements, consultez [journalisation avec msal.js](msal-logging.md).

- **loadFrameTimeout**: facultatif.  Le nombre de millisecondes d’inactivité avant qu’une réponse de renouvellement des jetons d’Azure AD doit être considéré comme expiré. Par défaut est de 6 secondes.

- **tokenRenewalOffsetSeconds**: facultatif. Le nombre de millisecondes qui définit la fenêtre du décalage nécessaire de renouveler le jeton avant l’expiration. Valeur par défaut est 300 millisecondes.

Ceux-ci sont uniquement applicables à être transmis en aval à partir de la bibliothèque MSAL Angular de wrapper :
- **unprotectedResources**: facultatif.  Tableau d’URI qui sont des ressources non protégées. MSAL attachera pas un jeton pour les demandes sortantes ayant ces URI. La valeur par défaut est `null`.

- **protectedResourceMap**: facultatif.  Ceci est le mappage des ressources à des étendues utilisées par MSAL pour joindre automatiquement des jetons d’accès dans les appels d’API web. Un seul jeton d’accès est obtenu pour la ressource. Donc vous pouvez mapper un chemin d’accès de ressource spécifique comme suit : {« https://graph.microsoft.com/v1.0/me», [« user.read »]}, ou l’URL de l’application de la ressource en tant que : {« https://graph.microsoft.com/», [« user.read », « mail.send »]}. Cela est nécessaire pour les appels CORS. La valeur par défaut est `null`.
