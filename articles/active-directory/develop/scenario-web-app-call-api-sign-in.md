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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881610"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Application web appelant des API web : Supprimer les comptes du cache de jeton lors de la déconnexion globale

Vous avez appris à ajouter une connexion à votre application web dans [Application web qui connecte les utilisateurs : Se connecter et se déconnecter](scenario-web-app-sign-user-sign-in.md).

La déconnexion est différente pour une application web qui appelle des API web. Quand l’utilisateur se déconnecte de votre application ou de n’importe quelle application, vous devez supprimer les jetons associés à cet utilisateur du cache de jeton.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercepter le rappel après la déconnexion unique

Pour effacer l’entrée du cache de jeton associée au compte déconnecté, votre application peut intercepter l’événement après déconnexion (`logout`). Les applications web stockent les jetons d’accès de chaque utilisateur dans un cache de jeton. En interceptant le rappel après déconnexion (`logout`), votre application web peut supprimer l’utilisateur du cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Pour ASP.NET Core, le mécanisme d’interception est illustré dans la méthode `AddMsal()` de [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

L’URL de déconnexion que vous avez précédemment inscrite pour votre application vous permet d’implémenter la déconnexion unique. Le point de terminaison de déconnexion (`logout`) appelle votre URL de déconnexion. Cet appel se produit si la déconnexion a démarrée à partir de votre application web, ou à partir d’une autre application web ou du navigateur. Pour plus d’informations, consultez [Déconnexion unique](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Le code pour `RemoveAccountAsync` est disponible à partir de [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

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
> [Acquérir un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Acquérir un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Acquérir un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Acquérir un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
