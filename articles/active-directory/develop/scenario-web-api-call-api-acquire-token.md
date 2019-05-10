---
title: Web API que les appels autres API web (acquérir un jeton pour l’application) - plateforme d’identité Microsoft
description: Découvrez comment générer une API que les appels d’autres web API (l’acquisition d’un jeton pour l’application) web.
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231099"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>API Web qui appelle des API - web acquérir un jeton pour l’application

Une fois que vous avez créé une application cliente d’objet, l’utiliser pour acquérir un jeton que vous pouvez utiliser pour appeler une API web.

## <a name="code-in-the-controller"></a>Dans le contrôleur de code

Voici un exemple de code qui sera appelée dans les actions des contrôleurs d’API, en appelant une API en aval (nommée todolist).

```CSharp
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

`BuildConfidentialClient()` est similaire à ce que vous avez vu dans l’article [API Web qui appelle des API - configuration de l’application web](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instancie `IConfidentialClientApplication` avec un cache qui contient uniquement les informations pour un seul compte. Le compte est fourni par le `GetAccountIdentifier` (méthode).

Le `GetAccountIdentifier` méthode utilise les revendications associées à l’identité de l’utilisateur pour lequel l’API web a reçu le jeton JWT :

```CSharp
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
> [Appeler une API web](scenario-web-api-call-api-call-api.md)
