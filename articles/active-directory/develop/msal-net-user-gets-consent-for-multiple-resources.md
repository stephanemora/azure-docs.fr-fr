---
title: Obtenir le consentement de plusieurs ressources (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: Découvrez comment un utilisateur peut obtenir le consentement préalable de plusieurs ressources à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e8bd9a86d5ec0d39a7f1c26adac52f41e6420283
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121987"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Utilisateur obtienne son consentement à plusieurs ressources à l’aide de MSAL.NET
Le point de terminaison Microsoft identity plateforme ne vous permet pas d’obtenir un jeton pour plusieurs ressources à la fois. Lorsque vous utilisez la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET), le paramètre étendues dans la méthode de jeton acquire doit contenir uniquement les étendues pour une seule ressource. Toutefois, vous pouvez consentir préalable à l’avance de plusieurs ressources en spécifiant les étendues supplémentaires à l’aide de la `.WithExtraScopeToConsent` méthode du générateur.

> [!NOTE]
> Obtenir le consentement pour plusieurs travaux de ressources pour la plateforme d’identité Microsoft, mais pas pour Azure AD B2C. Azure AD B2C prend en charge le consentement de l’administrateur uniquement, pas consentement de l’utilisateur.

Par exemple, si vous disposez de deux ressources ayant 2 étendues de chacun :

- https :\//mytenant.onmicrosoft.com/customerapi (avec des 2 étendues `customer.read` et `customer.write`)
- https :\//mytenant.onmicrosoft.com/vendorapi (avec des 2 étendues `vendor.read` et `vendor.write`)

Vous devez utiliser le `.WithExtraScopeToConsent` modificateur qui a le *extraScopesToConsent* paramètre comme indiqué dans l’exemple suivant :

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Cela vous obtiendrez un jeton d’accès pour la première API web. Ensuite, lorsque vous avez besoin pour accéder à la deuxième API web, vous pouvez acquérir en mode silencieux le jeton à partir du cache de jeton :

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
