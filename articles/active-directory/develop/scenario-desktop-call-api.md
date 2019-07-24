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
ms.openlocfilehash: 4abaf234d3b216e0f67501e5d2f2f5c3f874c5d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111241"
---
# <a name="desktop-app-that-calls-web-apis---call-a-web-api"></a>Application de bureau qui appelle des API web - Appeler une API web

Maintenant que vous avez un jeton, vous pouvez appeler une API web protégée.

## <a name="calling-a-web-api-from-net"></a>Appeler une API web à partir de .NET

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

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
