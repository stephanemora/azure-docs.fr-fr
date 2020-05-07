---
title: Appeler une API web à partir d’une application web - Plateforme d’identités Microsoft | Azure
description: Apprendre à générer une application web qui appelle des API web (appelant une API web protégée)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 12802ab6dcfbbe5a1c5576ab672ead864dd0b4ae
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559876"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Application web appelant des API web : Appeler une API web

Maintenant que vous avez un jeton, vous pouvez appeler une API web protégée.

## <a name="call-a-protected-web-api"></a>Appeler une API web protégée

L’appel d’une API web protégée dépend du langage et de l’infrastructure de votre choix :

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Voici le code simplifié pour l’action du `HomeController`. Ce code obtient un jeton pour appeler Microsoft Graph. Du code a été ajouté pour montrer comment appeler Microsoft Graph en tant qu’API REST. L’URL de l’API Microsoft Graph est fournie dans le fichier appsettings.json et lue dans une variable nommée `webOptions` :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 
  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```

> [!NOTE]
> Vous pouvez utiliser le même principe pour appeler une API web.
>
> La plupart des API web fournissent un Kit de développement logiciel (SDK) qui simplifie l’appel de l’API. C’est également le cas de Microsoft Graph. Dans l’article suivant, vous allez apprendre où trouver un tutoriel illustrant l’utilisation de l’API.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-web-app-call-api-production.md)
