---
title: Passer un état personnalisé dans les demandes d’authentification (bibliothèque d’authentification Microsoft pour JavaScript)
titleSuffix: Microsoft identity platform
description: Découvrez comment passer une valeur de paramètre d’état personnalisé dans une demande d’authentification à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 527c1937da8634f7448c82ca8c0331fb5cfb85e3
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150605"
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
};
```

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