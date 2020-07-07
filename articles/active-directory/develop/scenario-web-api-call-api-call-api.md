---
title: API web appelant des API web - Plateforme d’identités Microsoft | Azure
description: Découvrez comment générer une API web qui appelle des API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885087"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Une API web qui appelle des API web : Appeler une API

Après avoir obtenu un jeton, vous pouvez appeler une API web protégée. Pour ce faire, utilisez le contrôleur de votre API web.

## <a name="controller-code"></a>Code de contrôle

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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

# <a name="java"></a>[Java](#tab/java)

Le code suivant poursuit l’exemple de code illustré dans [Une API web qui appelle des API web : Acquérir un jeton pour l’application](scenario-web-api-call-api-acquire-token.md). Le code est appelé dans les actions des contrôleurs d’API. Il appelle l’API MS Graph en aval.

Une fois que vous avez acquis le jeton, utilisez-le comme un jeton du porteur pour appeler l’API en aval.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Aucun exemple illustrant ce flux avec MSAL Python n’est encore disponible.

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Une API web qui appelle des API web : Passer en production](scenario-web-api-call-api-production.md)
