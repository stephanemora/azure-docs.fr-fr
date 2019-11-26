---
title: Acquérir un jeton en mode silencieux (bibliothèque d’authentification Microsoft pour .NET)
titleSuffix: Microsoft identity platform
description: Découvrez comment acquérir un jeton d’accès en mode silencieux (à partir du cache de jeton) à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802931"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obtenir un jeton à partir du cache de jeton à l’aide de MSAL.NET

Lorsque vous acquérez un jeton d’accès à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET), le jeton est mis en cache. Lorsque l’application a besoin d’un jeton, elle doit tout d’abord appeler la méthode `AcquireTokenSilent` pour vérifier si un jeton acceptable est dans le cache. Dans de nombreux cas, il est possible d’acquérir un autre jeton avec d’autres étendues basées sur un jeton dans le cache. Il est aussi possible d’actualiser un jeton quand il arrive à expiration (étant donné que le cache de jetons contient également un jeton d’actualisation).

Le modèle recommandé consiste à commencer par appeler la méthode `AcquireTokenSilent`.  Si `AcquireTokenSilent` échoue, utilisez d’autres méthodes pour acquérir un jeton.

Dans l’exemple suivant, l’application essaie d’abord d’acquérir un jeton à partir du cache de jeton.  Si une exception `MsalUiRequiredException` est générée, l’application acquiert un jeton de manière interactive. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
