---
title: Effacer le cache de jeton (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment effacer le cache de jeton à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 51407c4c157fa171bc5fd18e8db1f97f677ed973
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695583"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Effacer le cache de jeton à l’aide de MSAL.NET

Lorsque vous [acquérez un jeton d’accès](msal-acquire-cache-tokens.md) à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET), le jeton est mis en cache. Lorsque l’application a besoin d’un jeton, elle doit tout d’abord appeler la méthode `AcquireTokenSilent` pour vérifier si un jeton acceptable est dans le cache. 

L’effacement du cache est effectué en supprimant les comptes du cache. Le cookie de session qui se trouve dans le navigateur n’est quant à lui pas supprimé.  L’exemple suivant instancie une application cliente publique, obtient les comptes de l’application et supprime les comptes.

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

Pour en savoir plus sur l’acquisition et la mise en cache des jetons, consultez [Acquérir un jeton d’accès](msal-acquire-cache-tokens.md).
