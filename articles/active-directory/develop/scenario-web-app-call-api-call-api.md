---
title: Application web qui appelle des API web (appel d’une API web) - Plateforme d’identité Microsoft
description: Apprendre à générer une application web qui appelle des API web (appel d’une API web)
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
ms.openlocfilehash: 3624f4e859081e53ee27b6f8415eb3f9b5a2a5fa
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785459"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Application web qui appelle des API web - Appeler une API web

Maintenant que vous avez un jeton, vous pouvez appeler une API web protégée.

## <a name="aspnet-core"></a>ASP.NET Core

Voici un code simplifié de l’action de `HomeController`. Ce code obtient un jeton pour appeler Microsoft Graph. Ce code de temps a été ajouté, montrant comment appeler Microsoft Graph comme une API REST. L’URL de l’API Graph est fournie dans le fichier `appsettings.json` et lit une variable nommée `webOptions` :

```JSon
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Vous pouvez utiliser le même principe pour appeler une API web.
>
> La plupart des API web fournissent un Kit de développement logiciel (SDK) qui simplifie leur appel. C’est également le cas de Microsoft Graph. Dans l’article suivant, vous découvrirez où rechercher un didacticiel illustrant ces aspects.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-web-app-call-api-production.md)
