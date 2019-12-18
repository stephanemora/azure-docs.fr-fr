---
title: Appeler une API web à partir d’une application mobile | Azure
titleSuffix: Microsoft identity platform
description: Apprendre à générer une application mobile qui appelle des API web (appel d’une API web)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: db58f94501590eb3150700d282377ec1b2378cea
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962540"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Application mobile qui appelle des API web - Appeler une API web

Une fois que votre application a connecté un utilisateur et reçu des jetons, MSAL expose plusieurs éléments d’information sur l’utilisateur, l’environnement de l’utilisateur et les jetons émis. Votre application peut utiliser ces valeurs pour appeler une API web ou afficher un message de bienvenue à l’utilisateur.

Tout d’abord, nous allons examiner le résultat MSAL. Puis, nous étudierons la manière d’utiliser un jeton d’accès à partir de `AuthenticationResult` ou `result` pour appeler une API web protégée.

## <a name="msal-result"></a>Résultat de MSAL
MSAL propose les valeurs suivantes : 

- `AccessToken`: utilisée pour appeler des API web protégées dans une requête HTTP porteur.
- `IdToken`: contient des informations utiles sur l’utilisateur connecté, telles que le nom d’utilisateur, le locataire de base et un identificateur unique de stockage.
- `ExpiresOn`: délai d’expiration du jeton. MSAL gère l’actualisation automatique des applications.
- `TenantId`: identificateur du locataire, auquel l’utilisateur est connecté. Pour les utilisateurs invités (Azure Active Directory B2B), cette valeur identifie le locataire auquel l’utilisateur est connecté et non le locataire de base de l’utilisateur.  
- `Scopes`: étendues qui ont été accordées avec votre jeton. Les étendues accordées peuvent être un sous-ensemble des étendues demandées.

MSAL fournit également une abstraction de `Account`. Un `Account` représente le compte de connexion de l’utilisateur actuel.

- `HomeAccountIdentifier`: identificateur du locataire de base de l’utilisateur.
- `UserName`: nom d’utilisateur préféré de l’utilisateur. Cette valeur peut être vide pour les utilisateurs Azure Active Directory B2C.
- `AccountIdentifier`: identificateur de l’utilisateur connecté. Cette valeur doit être identique à la valeur `HomeAccountIdentifier` dans la plupart des cas, sauf si l’utilisateur est un invité dans un autre locataire.

## <a name="call-an-api"></a>Appeler une API

Une fois que vous disposez du jeton d’accès, il est facile d’appeler une API web. Votre application utilisera le jeton pour construire une requête HTTP, puis exécuter la requête.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL pour iOS et macOS

Les méthodes d'acquisition de jetons renvoient un objet `MSALResult`. `MSALResult` expose une propriété `accessToken` qui peut être utilisée pour appeler une API web. Le jeton d'accès doit être ajouté à l'en-tête d'autorisation HTTP avant de passer l'appel pour accéder à l'API web protégée.

Objective-C :

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift :

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Faire plusieurs requêtes d’API

Si vous avez besoin d’appeler la même API plusieurs fois, ou plusieurs API, tenez compte des éléments suivants lorsque vous générez votre application :

- **Consentement incrémentiel** : La Plateforme d’identités Microsoft permet aux applications d’obtenir le consentement de l’utilisateur lorsque des autorisations sont requises, et non toutes les autorisations au début. Chaque fois que votre application est prête à appeler une API, elle doit demander uniquement les étendues qu’elle doit utiliser.
- **Accès conditionnel** : Dans certains scénarios, vous pouvez obtenir les exigences d’accès conditionnel supplémentaires lorsque vous effectuez plusieurs requêtes d’API. Cela peut se produire si la première requête n’a aucune stratégie d’accès conditionnel appliquée et que votre application tente d’accéder en mode silencieux à une nouvelle API qui requiert un accès conditionnel. Pour gérer ce scénario, veillez à intercepter les erreurs des requêtes en mode silencieux et à vous préparer à effectuer une requête interactive.  Pour plus d’informations, consultez [Recommandations en matière d’accès conditionnel](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Appel de plusieurs API dans Xamarin ou UWP – Consentement incrémentiel et accès conditionnel

Si vous devez appeler plusieurs API pour le même utilisateur, une fois que vous avez acquis un jeton pour un utilisateur, vous pouvez éviter de demander à l’utilisateur des informations d’identification à plusieurs reprises en appelant `AcquireTokenSilent` pour obtenir un jeton.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

L’interaction est requise dans les cas suivants :

- L’utilisateur a donné son consentement pour la première API, mais doit également le donner pour davantage d’étendues (consentement incrémentiel).
- La première API ne nécessitait aucune authentification multifacteur, contrairement à celle qui suit.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Passer en production](scenario-mobile-production.md)
