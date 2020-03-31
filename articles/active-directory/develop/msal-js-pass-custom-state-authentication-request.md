---
title: Passer un état personnalisé dans des demandes d’authentification (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment passer une valeur de paramètre d’état personnalisé dans une demande d’authentification à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084938"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passer un état personnalisé dans les demandes d’authentification avec MSAL.js

Le paramètre *state*, tel que défini par OAuth 2.0, est inclus dans une demande d’authentification et est également retourné dans la réponse de jeton pour empêcher les attaques par falsification de requêtes intersites. Par défaut, la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) passe une valeur unique, générée de manière aléatoire, du paramètre *state* dans les demandes d’authentification.

Le paramètre state peut également être utilisé pour coder les informations d’état d’une application avant la redirection. Vous pouvez passer l’état de l’utilisateur dans l’application, tel que la page ou la vue où il se trouvait, en tant qu’entrée sur ce paramètre. La bibliothèque MSAL.js vous permet de passer votre état personnalisé en tant que paramètre d’état dans l’objet `Request` :

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Si vous souhaitez ignorer un jeton mis en cache et accéder au serveur, transmettez la valeur booléenne `forceRefresh` dans l’objet AuthenticationParameters utilisé pour effectuer une demande de connexion/jeton.
> Par défaut, `forceRefresh` ne doit pas être utilisé en raison de l’impact sur les performances de votre application.
> L'utilisation du cache offre à vos utilisateurs une meilleure expérience.
> Ignorez uniquement le cache si vous savez que les données actuellement mises en cache ne contiennent pas d'informations à jour.
> Par exemple, un outil d’administration qui ajoute des rôles à un utilisateur et doit obtenir un nouveau jeton avec des rôles mis à jour.

Par exemple :

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

L’état passé est ajouté au GUID unique défini par MSAL.js lors de l’envoi de la demande. Lorsque la réponse est retournée, MSAL.js recherche une correspondance avec l’état, puis retourne l’état transmis personnalisé dans l’objet `Response` en tant que `accountState`.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Pour en savoir plus, consultez la [création d’une application monopage (SPA)](scenario-spa-overview.md) avec MSAL.js.