---
title: Passer l’état personnalisé dans les demandes d’authentification (bibliothèque d’authentification Microsoft pour JavaScript) | Azure
description: Découvrez comment passer une valeur de paramètre d’état personnalisé dans la demande d’authentification à l’aide de la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js).
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
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420497"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Passer l’état personnalisé dans les demandes d’authentification à l’aide de MSAL.js
Le *état* paramètre, tel que défini par OAuth 2.0, est inclus dans une demande d’authentification et est également retourné dans la réponse de jeton pour empêcher les attaques par falsification de requête intersites. Par défaut, la bibliothèque d’authentification Microsoft pour JavaScript (MSAL.js) passe généré aléatoirement unique *état* valeur de paramètre dans les demandes d’authentification.

Le paramètre d’état peut également être utilisé pour coder les informations d’état de l’application avant la redirection. Vous pouvez passer l’état de l’utilisateur dans l’application, telles que la page ou la vue qu’ils étaient sur, en tant qu’entrée à ce paramètre. La bibliothèque MSAL.js vous permet de transmettre votre état personnalisé en tant que paramètre d’état dans le `Request` objet :

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

Exemple :

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

L’état passé est ajouté au GUID unique défini par MSAL.js lors de l’envoi de la demande. Lorsque la réponse est renvoyée, MSAL.js recherche une correspondance avec état et retourne ensuite personnalisé passé à l’état dans le `Response` de l’objet en tant que `accountState`.

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

Pour en savoir plus, en savoir plus sur [création d’une application monopage (SPA)](scenario-spa-overview.md) utilisant MSAL.js.