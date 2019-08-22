---
title: Acquérir un jeton en mode silencieux (Microsoft Authentication Library pour .NET) | Azure
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
ms.openlocfilehash: 48a4c7a96e48ddf5a000888929b8bab719ff89fa
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532702"
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
