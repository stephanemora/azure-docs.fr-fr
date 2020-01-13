---
title: Supprimer des comptes du cache de jeton lors de la déconnexion – Plateforme d’identités Microsoft | Azure
description: Découvrir comment supprimer un compte du cache de jeton lors de la déconnexion
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5472b424f7d2b44b62e6e4495afaf7bdfbbc8439
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423508"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>Supprimer les comptes du cache lors de la déconnexion globale

Vous savez déjà comment ajouter des informations d’identification à votre application web. Vous l’avez appris dans la section relative à [l’application web qui connecte des utilisateurs - Ajouter des infos d’identification](scenario-web-app-sign-user-sign-in.md).

Ce qui est différent ici, c’est que, lorsque l’utilisateur se déconnecte, à partir de cette application ou de n’importe quelle application, il vous faudra supprimer du cache de jetons les jetons associés à l’utilisateur.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Interception du rappel après la déconnexion - Déconnexion unique

Votre application peut intercepter l’événement après `logout`, par exemple pour effacer l’entrée du cache de jeton associé au compte déconnecté. L’application web stocke les jetons d’accès de l’utilisateur dans un cache. Intercepter le rappel après `logout` permet à votre application web de supprimer l’utilisateur à partir du cache de jeton.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Ce mécanisme est illustré dans la méthode `AddMsal()` de [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

L’**URL de déconnexion** que vous avez inscrite pour votre application vous permet d’implémenter la déconnexion unique. Le point de terminaison `logout` de la plateforme Microsoft Identity appellera l’**URL de déconnexion** inscrite avec votre application. Cet appel se produit si la déconnexion a été initialisée à partir de votre application web, ou d’une autre application web ou du navigateur. Pour plus d’informations, consultez [Déconnexion unique](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

Le code pour RemoveAccountAsync est disponible à partir de [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

L’exemple ASP.NET ne supprime pas les comptes du cache lors de la déconnexion globale.

# <a name="javatabjava"></a>[Java](#tab/java)

L’exemple Java ne supprime pas les comptes du cache lors de la déconnexion globale.

# <a name="pythontabpython"></a>[Python](#tab/python)

L’exemple Python ne supprime pas les comptes du cache lors de la déconnexion globale.

---

## <a name="next-steps"></a>Étapes suivantes

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Acquisition d’un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Acquisition d’un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Acquisition d’un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Acquisition d’un jeton pour l’application web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
