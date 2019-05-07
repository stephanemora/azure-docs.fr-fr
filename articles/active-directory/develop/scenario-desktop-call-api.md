---
title: Application de bureau que les appels de web API (appeler une API web) - plateforme d’identité Microsoft
description: Découvrez comment créer une application de bureau qui appelle web API (en appelant une API web)
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
ms.openlocfilehash: 6fb240b54c3d698ead9d3427f603acca2b53745a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075924"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Application de bureau qui appelle des API - web appeler une API web

Maintenant que vous avez un jeton, vous pouvez appeler une API web protégée.

## <a name="calling-a-web-api-from-net"></a>Appeler une API web à partir de .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis---incremental-consent-and-conditional-access"></a>Appeler plusieurs API - consentement incrémentiel et l’accès conditionnel

Si vous avez besoin d’appeler plusieurs API pour le même utilisateur, une fois que vous avez obtenu un jeton pour la première API, vous pouvez simplement appeler `AcquireTokenSilent`, et vous obtenez un jeton pour les autres API en mode silencieux la plupart du temps.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Les cas où l’interaction n’est requise est cas suivants :

- L’utilisateur a donné son consentement pour la première API, mais désormais doit donner son consentement pour plus d’étendues (consentement incrémentiel)
- La première API sans nécessiter d’authentification à plusieurs facteurs, contrairement à celle qui suit.

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
