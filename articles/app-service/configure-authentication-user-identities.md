---
title: Identités d’utilisateur dans AuthN/AuthZ
description: Découvrez comment accéder aux identités d’utilisateur quand vous utilisez l’authentification et l’autorisation intégrées dans App Service.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: ae483e4213ec17cfa3a4532fbf615a40c760ad6e
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230429"
---
# <a name="work-with-user-identities-in-azure-app-service-authentication"></a>Utiliser des identités d’utilisateur dans l’authentification Azure App Service

Cet article explique comment utiliser les identités d’utilisateur quand vous utilisez [l’authentification et l’autorisation intégrées dans App Service](overview-authentication-authorization.md). 

## <a name="access-user-claims-in-app-code"></a>Accéder aux revendications de l’utilisateur dans le code de l’application

Pour toutes les infrastructures de langage, App Service met les revendications du jeton entrant (qu’elles proviennent d’un utilisateur final authentifié ou d’une application cliente) à la disposition de votre code en les injectant dans les en-têtes des demandes. Les demandes externes ne sont pas autorisées à définir ces en-têtes. Ces derniers ne s’affichent donc que s’ils sont définis par App Service. Voici quelques exemples d’en-têtes :

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Tout code, quels que soient le langage ou l’infrastructure utilisés, peut trouver les informations qu’il recherche dans ces en-têtes. 

> [!NOTE]
> Différentes infrastructures de langage peuvent présenter ces en-têtes au code d’application dans différents formats, tels que des minuscules ou des majuscules.

Dans le cas des applications ASP.NET 4.6, App Service remplit [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) avec les revendications de l’utilisateur authentifié, ce qui vous permet de suivre le modèle de code .NET standard, attribut `[Authorize]` compris. De même, pour les applications PHP, App Service remplit la variable `_SERVER['REMOTE_USER']`. Pour les applications Java, les revendications sont [accessibles depuis le servlet Tomcat](configure-language-java.md#authenticate-users-easy-auth).

Pour [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` n’est pas rempli pour le code .NET, mais vous pouvez toujours trouver les revendications de l’utilisateur dans les en-têtes de requête ou obtenir l’objet `ClaimsPrincipal` à partir du contexte de la requête, ou même via un paramètre de liaison. Consultez [Utilisation d’identités de clients dans Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) pour plus d’informations.

Pour .NET Core, [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) prend en charge le remplissage de l’utilisateur actuel à l’aide de l’authentification App Service. Pour en savoir plus, consultez le [wiki Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/wiki/1.2.0#integration-with-azure-app-services-authentication-of-web-apps-running-with-microsoftidentityweb), ou regardez une démonstration dans [ce tutoriel ayant trait à une application web qui accède à Microsoft Graph](./scenario-secure-app-access-microsoft-graph-as-user.md?tabs=command-line#install-client-library-packages).

## <a name="access-user-claims-using-the-api"></a>Accéder aux revendications de l’utilisateur à l’aide de l’API

Si le [magasin de jetons](overview-authentication-authorization.md#token-store) est activé pour votre application, vous pouvez également obtenir des informations supplémentaires sur l’utilisateur authentifié en appelant `/.auth/me`. Les Kits de développement logiciel (SDK) serveur de Mobile Apps offrent des méthodes d’assistance permettant de manipuler ces données. Pour plus d’informations, consultez [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity), et [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Authentifier et autoriser des utilisateurs de bout en bout](tutorial-auth-aad.md)