---
title: Obtenir le consentement de plusieurs ressources (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment un utilisateur peut obtenir le consentement préalable pour plusieurs ressources à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89afdbed6870b4ce739ed51131def686c41a3015
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921756"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>L’utilisateur obtient le consentement pour plusieurs ressources à l’aide de MSAL.NET
Le point de terminaison de la plateforme d’identités Microsoft ne vous permet pas d’obtenir un jeton pour plusieurs ressources à la fois. Lorsque vous utilisez la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET), le paramètre d’étendue dans la méthode d’acquisition de jetons doit contenir uniquement les étendues pour une seule ressource. Toutefois, vous pouvez accorder un consentement préalable à plusieurs ressources en spécifiant les étendues supplémentaires à l’aide de la méthode de générateur `.WithExtraScopeToConsent`.

> [!NOTE]
> L’obtention d’un consentement pour plusieurs ressources fonctionne pour la plateforme d’identités Microsoft, mais pas pour Azure AD B2C. Azure AD B2C prend en charge le consentement de l’administrateur uniquement, et pas le consentement de l’utilisateur.

Par exemple, si vous disposez de deux ressources ayant deux étendues chacune :

- https:\//mytenant.onmicrosoft.com/customerapi (avec 2 étendues `customer.read` et `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (avec 2 étendues `vendor.read` et `vendor.write`)

Vous devez utiliser le modificateur `.WithExtraScopeToConsent` qui comporte le paramètre *extraScopesToConsent* comme indiqué dans l’exemple suivant :

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

Cela vous permet d’obtenir un jeton d’accès pour la première API web. Ensuite, lorsque vous avez besoin d’accéder à la deuxième API web, vous pouvez acquérir silencieusement le jeton à partir du cache du jeton :

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
