---
title: 'Application à page unique (appeler une API web) : plateforme d’identité Microsoft'
description: Découvrez comment créer une application à page unique (appeler une API web)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545613"
---
# <a name="single-page-application---call-a-web-api"></a>Application monopage – appeler une API web

Nous vous recommandons d’appeler la méthode `acquireTokenSilent` pour acquérir ou renouveler un jeton d’accès avant d’appeler une API web. Une fois que vous avez un jeton, vous pouvez appeler une API web protégée.

## <a name="call-a-web-api"></a>Appeler une API web

### <a name="javascript"></a>JavaScript

Utilisez le jeton d’accès acquis comme support dans une requête HTTP pour appeler une API web telle que l’API Microsoft Graph. Par exemple :

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

Comme mentionné dans l’[acquisition d’une section de jetons](scenario-spa-acquire-token.md), le wrapper MSAL Angular tire parti de l’intercepteur HTTP pour acquérir automatiquement des jetons d’accès en mode silencieux et les attacher aux requêtes HTTP aux API.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-spa-production.md)
