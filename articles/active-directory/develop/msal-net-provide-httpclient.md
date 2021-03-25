---
title: Fournir un HttpClient et un proxy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez la fourniture de vos propres HttpClient et proxy pour vous connecter à Azure AD à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6139fd1b081c69f037ec9cd3313e4a6499c39543
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064621"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fourniture de vos propres HttpClient et proxy à l’aide de MSAL.NET
Lors de l’[initialisation d’une application cliente publique](msal-net-initializing-client-applications.md), vous pouvez utiliser la méthode `.WithHttpClientFactory method` pour fournir votre propre HttpClient.  La fourniture de votre propre HttpClient permet des scénarios avancés tels qu’un contrôle précis d’un proxy HTTP, la personnalisation des en-têtes de l’agent utilisateur ou l’obligation pour MSAL d’utiliser un HttpClient spécifique (par exemple dans les applications web/API ASP.NET Core).

## <a name="initialize-with-httpclientfactory"></a>Initialiser avec HttpClientFactory
L’exemple suivant montre comment créer un `HttpClientFactory` , puis initialiser une application cliente publique avec celui-ci :

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient et Xamarin iOS
Lorsque vous utilisez Xamarin iOS, il est recommandé de créer un `HttpClient` qui utilise explicitement le gestionnaire basé sur `NSURLSession` pour iOS 7 et versions ultérieures. MSAL.NET crée automatiquement un `HttpClient` qui utilise `NSURLSessionHandler` pour iOS 7 et versions ultérieures. Pour plus d’informations, consultez la [documentation Xamarin iOS pour HttpClient](/xamarin/cross-platform/macios/http-stack).