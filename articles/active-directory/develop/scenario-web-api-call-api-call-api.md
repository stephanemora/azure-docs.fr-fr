---
title: API web appelant des API web - Plateforme d’identités Microsoft | Azure
description: Découvrez comment générer une API web qui appelle des API web.
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
ms.openlocfilehash: 701f1f7c26731f3e9653955907f5f16d2688cdb2
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043436"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Une API web qui appelle des API web : Appeler une API

Après avoir obtenu un jeton, vous pouvez appeler une API web protégée. Pour ce faire, vous pouvez utiliser le contrôleur de votre API web ASP.NET ou ASP.NET Core.

## <a name="controller-code"></a>Code de contrôle

Le code suivant poursuit l’exemple de code illustré dans [Une API web qui appelle des API web : Acquérir un jeton pour l’application](scenario-web-api-call-api-acquire-token.md). Le code est appelé dans les actions des contrôleurs d’API. Il appelle une API en aval nommée *todolist*.

Une fois que vous avez acquis le jeton, utilisez-le comme un jeton du porteur pour appeler l’API en aval.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Une API web qui appelle des API web : Passer en production](scenario-web-api-call-api-production.md)
