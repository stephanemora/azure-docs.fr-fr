---
title: Application de bureau qui appelle des API web (appel d’une API web) - Plateforme d’identités Microsoft
description: Apprendre à générer une application de bureau qui appelle des API web (appel d’une API web)
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
ms.openlocfilehash: 56d3d01e39adfeb6bf2ef5e7e7d595f49c90f5a5
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268273"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Application de bureau qui appelle des API web - Appeler une API web

Maintenant que vous avez un jeton, vous pouvez appeler une API web protégée.

## <a name="calling-a-web-api-from-net"></a>Appeler une API web à partir de .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-a-web-api-in-msal-for-ios-and-macos"></a>Appeler une API web dans MSAL pour iOS et macOS

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

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Appel de plusieurs API - Consentement incrémentiel et accès conditionnel

Si vous avez besoin d’appeler plusieurs API pour le même utilisateur, une fois que vous avez obtenu un jeton pour la première API, vous pouvez simplement appeler `AcquireTokenSilent` ; vous obtiendrez un jeton pour les autres API en mode silencieux la plupart du temps.

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
> [Passer en production](scenario-desktop-production.md)
