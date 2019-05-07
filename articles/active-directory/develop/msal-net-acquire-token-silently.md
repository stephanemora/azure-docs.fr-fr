---
title: Acquérir un jeton en mode silencieux (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: Découvrez comment acquérir un jeton d’accès à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET) en mode silencieux (cache de jetons).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e49dd85503475feab81a14148588981b04ad954
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158849"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Obtenir un jeton à partir du cache de jeton à l’aide de MSAL.NET

Lorsque vous achetez un jeton d’accès à l’aide de Microsoft Authentication Library pour .NET (MSAL.NET), le jeton est mis en cache. Lorsque l’application a besoin d’un jeton, elle doit tout d’abord appeler la `AcquireTokenSilent` méthode pour vérifier si un jeton acceptable est dans le cache. Dans de nombreux cas, il est possible d’acquérir un autre jeton avec des étendues basées sur un jeton dans le cache. Il est également possible d’actualiser un jeton lorsque l’échéance arrive à expiration (comme le cache de jetons contient également un jeton d’actualisation).

Le modèle recommandé consiste à appeler le `AcquireTokenSilent` méthode première.  Si `AcquireTokenSilent` échoue, puis acquérir un jeton à l’aide d’autres méthodes.

Dans l’exemple suivant, l’application essaie d’abord d’acquérir un jeton à partir du cache de jeton.  Si un `MsalUiRequiredException` exception est levée, l’application acquiert un jeton de manière interactive. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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