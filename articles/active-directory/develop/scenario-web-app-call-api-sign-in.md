---
title: Supprimer des comptes du cache de jeton lors de la déconnexion - Plateforme d’identités Microsoft | Azure
description: Découvrir comment supprimer un compte du cache de jeton lors de la déconnexion
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5246100815fde569c55027a555464c44a240d4b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119995"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Application web appelant des API web : Supprimer les comptes du cache de jeton lors de la déconnexion globale

Vous avez appris à ajouter une connexion à votre application web dans [Application web qui connecte les utilisateurs : Se connecter et se déconnecter](scenario-web-app-sign-user-sign-in.md).

La déconnexion est différente pour une application web qui appelle des API web. Quand l’utilisateur se déconnecte de votre application ou de n’importe quelle application, vous devez supprimer les jetons associés à cet utilisateur du cache de jeton.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercepter le rappel après la déconnexion unique

Pour effacer l’entrée du cache de jeton associée au compte déconnecté, votre application peut intercepter l’événement après déconnexion (`logout`). Les applications web stockent les jetons d’accès de chaque utilisateur dans un cache de jeton. En interceptant le rappel après déconnexion (`logout`), votre application web peut supprimer l’utilisateur du cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web prend en charge l’implémentation de la déconnexion. Pour plus d’informations, consultez [Code source de Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

L’exemple ASP.NET ne supprime pas les comptes du cache lors de la déconnexion globale.

# <a name="java"></a>[Java](#tab/java)

L’exemple Java ne supprime pas les comptes du cache lors de la déconnexion globale.

# <a name="python"></a>[Python](#tab/python)

L’exemple Python ne supprime pas les comptes du cache lors de la déconnexion globale.

---

## <a name="next-steps"></a>Étapes suivantes

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Acquérir un jeton pour l’application web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Acquérir un jeton pour l’application web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Acquérir un jeton pour l’application web](./scenario-web-app-call-api-acquire-token.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Acquérir un jeton pour l’application web](./scenario-web-app-call-api-acquire-token.md?tabs=python)

---