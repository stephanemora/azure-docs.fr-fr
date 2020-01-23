---
title: Obtenir un jeton pour une API web appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment générer une API web qui appelle des API web nécessitant l’acquisition d’un jeton pour l’application.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cf660cbf981079ca20111e34fcd34504d8dcbfb
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044118"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Une API web qui appelle des API web : Acquérir un jeton pour l’application

Après avoir généré un objet d’application cliente, utilisez-le pour acquérir un jeton qui vous servira à appeler une API web.

## <a name="code-in-the-controller"></a>Code dans le contrôleur

Voici un exemple de code appelé dans les actions des contrôleurs d’API. Il appelle une API en aval nommée *todolist*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` est semblable au scénario présenté dans [Une API web qui appelle des API web : Configuration d’application](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instancie `IConfidentialClientApplication` avec un cache qui contient les informations d’un seul compte. Le compte est fourni par la méthode `GetAccountIdentifier`.

La méthode `GetAccountIdentifier` utilise les revendications qui sont associées à l’identité de l’utilisateur pour lequel l’API web a reçu le jeton JWT (JSON Web Token) :

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Une API web qui appelle des API web : Appeler une API](scenario-web-api-call-api-call-api.md)
