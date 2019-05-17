---
title: Effacer le cache de jeton à l’aide de la bibliothèque d’authentification Microsoft pour .NET - Azure
description: Découvrez comment effacer le cache de jeton à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6763c6b2b1f9b4de7d8669a50a4979a7aac00c7
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544120"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Effacer le cache de jeton à l’aide de MSAL.NET

Lorsque vous [acquérir un jeton d’accès](msal-acquire-cache-tokens.md) à l’aide de Microsoft Authentication Library pour .NET (MSAL.NET), le jeton est mis en cache. Lorsque l’application a besoin d’un jeton, elle doit tout d’abord appeler la `AcquireTokenSilent` méthode pour vérifier si un jeton acceptable est dans le cache. 

Effacement du cache est obtenue en supprimant les comptes à partir du cache. Le cookie de session qui se trouve dans le navigateur n’est quant à lui pas supprimé.  L’exemple suivant instancie une application cliente publique Obtient les comptes pour l’application et supprime les comptes.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Pour en savoir plus sur l’acquisition et la mise en cache des jetons, consultez [acquérir un jeton d’accès](msal-acquire-cache-tokens.md).
