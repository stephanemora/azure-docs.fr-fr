---
title: Application Web par appels web API (se connecter) - plateforme d’identité Microsoft
description: Découvrez comment créer une application Web qui appelle des API (connexion) web
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074559"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Application Web qui appelle des API web - connexion

Vous savez déjà comment ajouter une connexion à votre application web. Vous apprenez que dans [application Web que les utilisateurs se connecte en - ajouter une connexion](scenario-web-app-sign-user-sign-in.md).

Ce qui est différent ici, est que lorsque l’utilisateur a connecté out, à partir de cette application ou à partir de n’importe quelle application, vous souhaitez supprimer du cache de jetons, les jetons associés à l’utilisateur.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Intercepter le rappel après déconnexion - Single Sign Out

Votre application peut intercepter l’après `logout` événement, par exemple pour effacer l’entrée du cache de jeton associé au compte déconnecté. Nous verrons dans la deuxième partie de ce didacticiel (sur l’application Web appelant une API Web), que l’application web stocke les jetons d’accès pour l’utilisateur dans un cache. Intercepter l’après `logout` rappel permet à votre application web supprimer l’utilisateur à partir du cache de jeton. Ce mécanisme est illustré dans le `AddMsal()` méthode de [StartupHelper.cs L137-143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

Le **Url de déconnexion** que vous avez inscrit pour votre application vous permet d’implémenter la déconnexion unique. La plateforme Microsoft identity `logout` point de terminaison appellera le **URL de déconnexion** inscrit avec votre application. Cet appel se produit si la déconnexion a été initialisée à partir de votre application web, ou à partir d’une autre application web ou le navigateur. Pour plus d’informations, consultez [déconnexion unique](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) dans la documentation conceptuelle.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [L’acquisition d’un jeton pour l’application web](scenario-web-app-call-api-acquire-token.md)
