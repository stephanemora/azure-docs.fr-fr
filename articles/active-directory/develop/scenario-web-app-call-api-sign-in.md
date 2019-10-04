---
title: Application web qui appelle des API web (connexion) - Plateforme d’identités Microsoft
description: Apprenez à générer une application web qui appelle des API web (connexion)
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074559"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Application web qui appelle des API web - Connexion

Vous savez déjà comment ajouter des informations d’identification à votre application web. Vous l’avez appris dans la section relative à [l’application web qui connecte des utilisateurs - Ajouter des infos d’identification](scenario-web-app-sign-user-sign-in.md).

Ce qui est différent ici, c’est que, lorsque l’utilisateur se déconnecte, à partir de cette application ou de n’importe quelle application, il vous faudra supprimer du cache de jetons les jetons associés à l’utilisateur.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>Intercepter le rappel après la déconnexion - Déconnexion unique

Votre application peut intercepter l’événement après `logout`, par exemple pour effacer l’entrée du cache de jeton associé au compte déconnecté. Nous verrons dans la deuxième partie de ce didacticiel (sur l’application web appelant une API web), que l’application web stocke les jetons d’accès de l’utilisateur dans un cache. Intercepter le rappel après `logout` permet à votre application web de supprimer l’utilisateur à partir du cache de jeton. Ce mécanisme est illustré dans la méthode `AddMsal()` du [StartupHelper.cs L137-143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

L’**URL de déconnexion** que vous avez inscrite pour votre application vous permet d’implémenter la déconnexion unique. Le point de terminaison `logout` de la plateforme Microsoft Identity appellera l’**URL de déconnexion** inscrite avec votre application. Cet appel se produit si la déconnexion a été initialisée à partir de votre application web, ou d’une autre application web ou du navigateur. Pour plus d’informations, consultez [Déconnexion unique](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) dans la documentation conceptuelle.

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
> [Acquisition d’un jeton pour l’application web](scenario-web-app-call-api-acquire-token.md)
