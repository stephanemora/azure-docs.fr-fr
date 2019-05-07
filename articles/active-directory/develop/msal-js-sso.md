---
title: Authentification unique (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: En savoir plus sur la création d’une expérience de connexion unique à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075849"
---
# <a name="single-sign-on-with-msaljs"></a>L’authentification unique avec MSAL.js

L’authentification unique (SSO) permet aux utilisateurs d’entrer leurs informations d’identification une seule fois pour se connecter et établir une session qui peut être réutilisée dans plusieurs applications sans avoir à s’authentifier à nouveau. Cela offre une expérience transparente à l’utilisateur et réduit les invites répétées pour des informations d’identification.

Azure AD fournit les fonctionnalités de l’authentification unique pour les applications en définissant un cookie de session lorsque l’utilisateur s’authentifie auprès de la première fois. La bibliothèque MSAL.js permet aux applications de tirer parti de cette de plusieurs façons.

## <a name="sso-between-browser-tabs"></a>Authentification unique entre les onglets de navigateur

Lorsque votre application est ouverte dans plusieurs onglets et vous connectez tout d’abord l’utilisateur sur un onglet, l’utilisateur est également connecté sur les autres onglets sans avoir à saisir. MSAL.js met en cache le jeton d’ID de l’utilisateur dans le navigateur `localStorage` et se connecteront l’utilisateur à l’application sur les autres onglets ouverts.

Par défaut, utilise MSAL.js `sessionStorage` qui n’autorise pas la session à partager entre les onglets. Pour configurer l’authentification unique entre les onglets, veillez à définir le `cacheLocation` dans MSAL.js à `localStorage` comme indiqué ci-dessous.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Authentification unique entre applications

Lorsqu’un utilisateur s’authentifie, un cookie de session est défini sur le domaine Azure AD dans le navigateur. MSAL.js s’appuie sur ce cookie de session pour fournir l’authentification unique de l’utilisateur entre les différentes applications. MSAL.js met également en cache les jetons d’ID et jetons d’accès de l’utilisateur dans le navigateur de stockage par domaine d’application. Par conséquent, le comportement de l’authentification unique varie pour les différents cas :  

### <a name="applications-on-the-same-domain"></a>Applications sur le même domaine

Lorsque les applications sont hébergées sur le même domaine, l’utilisateur peut se connecter à une application une seule fois et alors authentifiées aux autres applications sans invite. MSAL.js s’appuie sur les jetons mis en cache pour l’utilisateur sur le domaine fournir l’authentification unique.

### <a name="applications-on-different-domain"></a>Applications sur un autre domaine

Lorsque les applications sont hébergées sur des domaines différents, les jetons mis en cache sur le domaine A ne sont pas accessibles par MSAL.js dans domaine B.

Cela signifie que lorsque les utilisateurs connectés sur le domaine pour accéder à une application sur le domaine B, ils ne doit être redirigés ou invités avec la page Azure AD. Dans la mesure où Azure AD a toujours le cookie de session utilisateur, elle déconnecte l’utilisateur, et ils ne doivent pas entrer à nouveau les informations d’identification. Si l’utilisateur dispose de plusieurs comptes d’utilisateur dans la session avec Azure AD, l’utilisateur sera invité à choisir le compte en question pour vous connecter à l’aide.

### <a name="automatically-select-account-on-azure-ad"></a>Sélectionner automatiquement le compte sur Azure AD

Dans certains cas, l’application a accès au contexte de l’authentification de l’utilisateur et souhaite éviter l’invite de sélection du compte Azure AD lorsque plusieurs comptes sont connectés.  Cela est possible de plusieurs manières :

**À l’aide de l’ID de Session (SID)**

ID de session est un [revendication facultative](active-directory-optional-claims.md) qui peut être configuré dans les jetons d’ID. Cette revendication permet à l’application identifier Azure l’utilisateur session AD indépendante du nom du compte d’utilisateur ou nom d’utilisateur. Vous pouvez transmettre l’identificateur de sécurité dans les paramètres de demande pour le `acquireTokenSilent` appeler. Cela permettra à Azure AD ignorer la sélection de compte. SID est liée au cookie de session et ne dépassera pas les contextes de navigateur.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID peut être utilisé uniquement avec l’authentification en mode silencieux les demandes effectuées par `acquireTokenSilent` appeler dans MSAL.js.
Vous trouverez les étapes de configuration des revendications facultatives dans votre manifeste d’application [ici](active-directory-optional-claims.md).

**À l’aide d’indicateur de connexion**

Si vous n’avez pas de SID de revendication configuré ou avez besoin ignorer l’invite de sélection de compte dans les appels de l’authentification interactive, vous pouvez le faire en fournissant un `login_hint` dans les paramètres de demande et éventuellement un `domain_hint` comme `extraQueryParameters` dans le MSAL.js des méthodes interactives (`loginPopup`, `loginRedirect`, `acquireTokenPopup` et `acquireTokenRedirect`). Par exemple : 

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Vous pouvez obtenir les valeurs pour login_hint et domain_hint en lisant les revendications retournées dans le jeton d’ID de l’utilisateur.

* **loginHint** doit être défini sur le `preferred_username` dans le jeton d’ID de revendication.

* **domain_hint** ne doit être passé lors de l’utilisation / Common autorité. L’indication de domaine est déterminée par le locataire ID(tid).  Si le `tid` revendication dans le jeton d’ID est `9188040d-6c67-4c5b-b112-36a304b66dad` il s’agit des consommateurs. Sinon, il est d’organisations.

Lecture [ici](v2-oauth2-implicit-grant-flow.md) pour plus d’informations sur les valeurs d’indicateur de connexion et indication de domaine.

> [!Note]
> Vous ne pouvez pas passer des SID et login_hint en même temps. Cela entraîne la réponse d’erreur.

## <a name="sso-without-msaljs-login"></a>Authentification unique sans connexion de MSAL.js

Par conception, MSAL.js requiert qu’une méthode de connexion est appelée pour établir un contexte utilisateur avant d’obtenir des jetons pour les API. Dans la mesure où les méthodes de connexion sont interactifs, l’utilisateur voit une invite de commandes.

Il existe certains cas dans lequel les applications ont accès au contexte de l’utilisateur authentifié ou le jeton d’ID via l’authentification initiée par une autre application et souhaitez tirer parti de l’authentification unique pour acquérir des jetons sans la première se connectant via MSAL.js.

Est un exemple de ceci : Un utilisateur est connecté à l’application web parente qui héberge une autre application JavaScript en cours d’exécution en tant que module complémentaire ou plug-in.

L’expérience de l’authentification unique dans ce scénario peut être obtenue comme suit :

Passer le `sid` si elle est disponible (ou `login_hint` et éventuellement `domain_hint`) en tant que paramètres à la MSAL.js demande `acquireTokenSilent` appeler comme suit :

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Authentification unique dans ADAL.js à la mise à jour MSAL.js

MSAL.js apporte les mêmes fonctionnalités que ADAL.js pour les scénarios d’authentification Azure AD. Pour faciliter la migration à partir de ADAL.js pour MSAL.js pour éviter les invites de vos utilisateurs à se connecter à nouveau, la bibliothèque lit le jeton d’ID représentant la session de l’utilisateur dans le cache de ADAL.js et se connecte en toute transparence MSAL.js l’utilisateur.  

Pour tirer parti du comportement de l’authentification unique (SSO) lors de la mise à jour à partir de ADAL.js, vous devez vous assurer que les bibliothèques sont le `localStorage` pour la mise en cache des jetons. Définir le `cacheLocation` à `localStorage` dans la MSAL.js ADAL.js configuration et lors de l’initialisation comme suit :


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Une fois que cela est configuré, MSAL.js sera en mesure de lire l’état mis en cache de l’utilisateur authentifié dans ADAL.js et l’utiliser pour fournir l’authentification unique dans MSAL.js.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [unique la session d’authentification et de durée de vie](active-directory-configurable-token-lifetimes.md) valeurs dans Azure AD.
