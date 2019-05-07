---
title: Application de bureau que les appels API web (passer à la production) - plateforme d’identité Microsoft
description: Découvrez comment créer une application de bureau que les appels de web API (passer à la production)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ca66a41f26c54bf04273682d14889a36b688c70
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075129"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Application de bureau qui appelle web API - passer en production

Cet article vous fournit des détails pour améliorer davantage votre application et déplacez-le vers la production.

## <a name="handling-errors-in-desktop-applications"></a>Gestion des erreurs dans les applications de bureau

Dans les différents flux, vous avez appris comment gérer les erreurs pour les flux en mode silencieux (comme indiqué dans les extraits de code). Nous avons également vu qu’il existe des cas où l’interaction est nécessaire (consentement incrémentiel et l’accès conditionnel).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Comment obtenir le consentement de l’utilisateur dès le départ pour plusieurs ressources

> [!NOTE]
> Obtenir le consentement pour plusieurs travaux de ressources pour la plateforme d’identité Microsoft, mais pas pour Azure Active Directory (Azure AD) B2C. Azure AD B2C prend en charge le consentement de l’administrateur uniquement, pas consentement de l’utilisateur.

Le point de terminaison Microsoft identity platform (v2.0) ne vous permet d’obtenir un jeton pour plusieurs ressources à la fois. Par conséquent, le `scopes` paramètre peut contenir uniquement des étendues pour une seule ressource. Vous pouvez vous assurer que l’utilisateur préalablement donne son consentement à plusieurs ressources à l’aide de le `extraScopesToConsent` paramètre.

Par exemple, si vous avez deux ressources, qui ont deux étendues de chacun :

- `https://mytenant.onmicrosoft.com/customerapi` -avec des 2 étendues `customer.read` et `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` -avec des 2 étendues `vendor.read` et `vendor.write`

Vous devez utiliser le `.WithAdditionalPromptToConsent` modificateur qui a le `extraScopesToConsent` paramètre.

Exemple :

```CSharp
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

Cet appel, vous obtiendrez un jeton d’accès pour la première API web.

Lorsque vous avez besoin d’appeler l’API web de deuxième, vous pouvez appeler :

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Compte personnel Microsoft nécessite reconsenting chaque fois que l’application est exécutée.

Pour les utilisateurs de comptes personnels Microsoft, reprompting à donner son consentement à chaque appel client natif (application de bureau/mobile) pour autoriser le comportement est voulu. Identité du client natif est sécurisée par nature (contrairement à l’application cliente confidentielle lequel échanger une clé secrète avec la plateforme Microsoft Identity pour prouver leur identité). La plateforme d’identité Microsoft a choisi d’atténuer cette insécurité pour les services de consommateur en invitant l’utilisateur à fournir son consentement, chaque fois que l’application est autorisée.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
