---
title: Application à page unique (appeler une API web) - plateforme d’identité Microsoft
description: Découvrez comment créer une application à page unique (appeler une API web)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074844"
---
# <a name="single-page-application---call-a-web-api"></a>Appelle une API web - application à page unique

Nous vous recommandons d’appeler le `acquireTokenSilent` méthode à acquérir ou renouveler un jeton d’accès avant d’appeler une API web. Une fois que vous avez un jeton, vous pouvez appeler une API web protégée.

## <a name="call-a-web-api"></a>Appeler une API web

### <a name="javascript"></a>JavaScript

Utiliser le jeton d’accès acquis comme un support dans une requête HTTP pour appeler une API web telles que les API Microsoft Graph. Par exemple : 

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

### <a name="angular"></a>Angular

Comme mentionné dans le [l’acquisition de section jetons](scenario-spa-acquire-token.md), le wrapper MSAL Angular tire parti de l’intercepteur HTTP pour acquérir des jetons d’accès en mode silencieux et joignez-les aux requêtes HTTP aux API automatiquement.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-spa-production.md)
