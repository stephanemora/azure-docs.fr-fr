---
title: Générer une application monopage appelant une API web - Plateforme d’identités Microsoft | Azure
description: Découvrir comment générer une application monopage qui appelle une API web
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
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159996"
---
# <a name="single-page-application-call-a-web-api"></a>Application monopage : Appeler une API web

Nous vous recommandons d’appeler la méthode `acquireTokenSilent` pour acquérir ou renouveler un jeton d’accès avant d’appeler une API web. Après avoir obtenu un jeton, vous pouvez appeler une API web protégée.

## <a name="call-a-web-api"></a>Appeler une API web

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Utilisez le jeton d’accès acquis, en tant que porteur dans une requête HTTP, pour appeler une API web, telle que l’API Microsoft Graph. Par exemple :

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

# <a name="angular"></a>[Angular](#tab/angular)

Le wrapper MSAL Angular tire parti de l’intercepteur HTTP pour acquérir automatiquement des jetons d’accès en mode silencieux, et les joindre aux requêtes HTTP destinées aux API. Pour plus d’informations, consultez [Acquérir un jeton pour appeler une API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-spa-production.md)
