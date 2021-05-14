---
title: Utiliser MSAL dans une application cloud nationale | Azure
titleSuffix: Microsoft identity platform
description: La bibliothèque d’authentification Microsoft (MSAL) permet aux développeurs d’applications d’acquérir des jetons afin d’appeler des API web sécurisées. Ces API web peuvent être Microsoft Graph, d’autres API Microsoft, des API web de partenaires ou vos propres API web. MSAL prend en charge plusieurs architectures et plateformes d’application.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: c1ecf807d566fd6603f12ebc820c176edf96ec14
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108071837"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Utiliser MSAL dans un environnement de cloud national

Les [clouds nationaux](authentication-national-cloud.md), également appelés clouds souverains, sont des instances d’Azure physiquement isolées. Ces régions d’Azure aident à garantir que les conditions de résidence, de souveraineté et de conformité des données sont respectées au sein de limites géographiques.

En plus du cloud Microsoft global, Microsoft Authentication Library (MSAL) permet aux développeurs d’applications dans des clouds nationaux d’acquérir des jetons pour authentifier et appeler des API web sécurisées. Ces API web peuvent être Microsoft Graph ou d’autres API Microsoft.

Outre le cloud global, Azure Active Directory (Azure AD) est déployé dans les clouds nationaux suivants :  

- Azure Government
- Azure China 21Vianet
- Azure Germany

Ce guide montre comment se connecter à des comptes professionnels et scolaires, obtenir un jeton accès et appeler l’API Microsoft Graph dans l’environnement [cloud Azure Government](https://azure.microsoft.com/global-infrastructure/government/).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous répondez aux exigences suivantes.

### <a name="choose-the-appropriate-identities"></a>Choisir les identités appropriées

Les applications [Azure Government](../../azure-government/index.yml) peuvent utiliser des identités Azure AD Government et des identités Azure AD Public pour authentifier des utilisateurs. Étant donné que vous pouvez utiliser l’une de ces identités, vous devez déterminer quel point de terminaison d’autorité à choisir pour votre scénario :

- Azure AD Public : généralement utilisé si votre organisation possède déjà un locataire Azure AD Public pour prendre en charge Microsoft 365 (Public ou GCC) ou une autre application.
- Azure AD Government : généralement utilisé si votre organisation dispose d’un locataire Azure AD Government pour prendre en charge Office 365 (GCC High ou DoD) ou si elle crée un nouveau locataire dans Azure Government AD.

Après avoir choisi, une attention particulière est portée là où vous effectuez votre inscription d’application. Si vous choisissez des identités Azure AD Public pour votre application Azure Government, vous devez inscrire l’application dans votre locataire Azure AD Public.

### <a name="get-an-azure-government-subscription"></a>Obtenir un abonnement Azure Government

Pour obtenir un abonnement Azure Government, consultez [Gestion et connexion à votre abonnement dans Azure Government](../../azure-government/compare-azure-government-global-azure.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/global-infrastructure/government/request/) avant de commencer.

Pour plus d’informations sur l’utilisation d’un cloud national avec un langage de programmation particulier, choisissez l’onglet correspondant à votre langage :

## <a name="net"></a>[.NET](#tab/dotnet)

Vous pouvez utiliser MSAL.NET pour connecter des utilisateurs, acquérir des jetons et appeler l’API Microsoft Graph dans des clouds nationaux.

Les didacticiels suivants montrent comment créer une application Web MVC .NET Core 2.2. L’application utilise OpenID Connect pour connecter des utilisateurs avec un compte professionnel et scolaire dans une organisation appartenant à un cloud national.

- Pour connecter des utilisateurs et acquérir des jetons, suivez ce didacticiel : [Créez une application web de base ASP.NET en connectant les utilisateurs dans des clouds souverains avec la plateforme d’identité Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Pour appeler l’API Microsoft Graph, suivez ce didacticiel : [Utilisation de la plateforme Microsoft Identity pour appeler l’API Microsoft Graph à partir d’une application web ASP.NET Core 2.x, pour le compte d’un utilisateur qui se connecte à l’aide de son compte professionnel et scolaire dans le cloud national Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Pour activer votre application MSAL.js pour les clouds souverains :

- Inscrivez votre application dans un portail spécifique en fonction du cloud. Pour plus d’informations sur la façon de choisir le portail, reportez-vous à [Points de terminaison d’inscription d’application](authentication-national-cloud.md#app-registration-endpoints)
- Utilisez n’importe lequel des [exemple](https://github.com/Azure-Samples/ms-identity-javascript-tutorial) du référentiel en apportant quelques modifications à la configuration, en fonction du cloud, comme mentionné ci-dessous.
- Utilisez une autorité spécifique, en fonction du cloud dans lequel vous avez inscrit l’application. Pour plus d’informations sur les autorités pour les différents clouds, consultez [Points de terminaison Azure AD Authentication](authentication-national-cloud.md#azure-ad-authentication-endpoints).
- L’appel de l’API Microsoft Graph nécessite une URL de point de terminaison spécifique au cloud que vous utilisez. Pour rechercher des points de terminaison Microsoft Graph pour tous les clouds nationaux, consultez [Points de terminaison racine de service de jetons et d’inscription de l’application](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Voici un exemple d’autorité :

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

Voici un exemple de point de terminaison Microsoft Graph, avec l’étendue :

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

Voici le code minimal pour l’authentification d’un utilisateur avec cloud souverain et appelant Microsoft Graph :

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        redirectUri: "/",
    }
};

// Initialize MSAL
const msalObj = new PublicClientApplication(msalConfig);

// Get token using popup experience
try {
    const graphToken = await msalObj.acquireTokenPopup({
        scopes: ["User.Read"]
    });
} catch(error) {
    console.log(error)
}

// Call the Graph API
const headers = new Headers();
const bearer = `Bearer ${graphToken}`;

headers.append("Authorization", bearer);

fetch("https://graph.microsoft.us/v1.0/me", {
    method: "GET",
    headers: headers
})
```


## <a name="python"></a>[Python](#tab/python)

Pour activer votre application MSAL Python pour les clouds souverains :

- Inscrivez votre application dans un portail spécifique en fonction du cloud. Pour plus d’informations sur la façon de choisir le portail, reportez-vous à [Points de terminaison d’inscription d’application](authentication-national-cloud.md#app-registration-endpoints)
- Utilisez n’importe lequel des [exemple](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) du référentiel en apportant quelques modifications à la configuration, en fonction du cloud, comme mentionné ci-dessous.
- Utilisez une autorité spécifique, en fonction du cloud dans lequel vous avez inscrit l’application. Pour plus d’informations sur les autorités pour les différents clouds, consultez [Points de terminaison Azure AD Authentication](authentication-national-cloud.md#azure-ad-authentication-endpoints).

    Voici un exemple d’autorité :

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- L’appel de l’API Microsoft Graph nécessite une URL de point de terminaison spécifique au cloud que vous utilisez. Pour rechercher des points de terminaison Microsoft Graph pour tous les clouds nationaux, consultez [Points de terminaison racine de service de jetons et d’inscription de l’application](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

    Voici un exemple de point de terminaison Microsoft Graph, avec l’étendue :

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Pour activer votre application MSAL pour Java pour les clouds souverains :

- Inscrivez votre application dans un portail spécifique en fonction du cloud. Pour plus d’informations sur la façon de choisir le portail, reportez-vous à [Points de terminaison d’inscription d’application](authentication-national-cloud.md#app-registration-endpoints)
- Utilisez n’importe lequel des [exemples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) du référentiel en apportant quelques modifications à la configuration, en fonction du cloud, comme mentionné ci-dessous.
- Utilisez une autorité spécifique, en fonction du cloud dans lequel vous avez inscrit l’application. Pour plus d’informations sur les autorités pour les différents clouds, consultez [Points de terminaison Azure AD Authentication](authentication-national-cloud.md#azure-ad-authentication-endpoints).

Voici un exemple d’autorité :

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- L’appel de l’API Microsoft Graph nécessite une URL de point de terminaison spécifique au cloud que vous utilisez. Pour rechercher des points de terminaison Microsoft Graph pour tous les clouds nationaux, consultez [Points de terminaison racine de service de jetons et d’inscription de l’application](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

Voici un exemple de point de terminaison Graph, avec l’étendue :

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

MSAL pour iOS et macOS peut être utilisé pour acquérir des jetons dans les clouds ​​nationaux, mais une configuration supplémentaire est nécessaire lors de la création de `MSALPublicClientApplication`.

Par exemple, si vous souhaitez que votre application soit mutualisée dans un cloud national (US Government, en l’occurrence), vous pouvez écrire :

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MSAL pour iOS et macOS peut être utilisé pour acquérir des jetons dans les clouds ​​nationaux, mais une configuration supplémentaire est nécessaire lors de la création de `MSALPublicClientApplication`.

Par exemple, si vous souhaitez que votre application soit mutualisée dans un cloud national (US Government, en l’occurrence), vous pouvez écrire :

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Étapes suivantes

Consultez [Points de terminaison d’authentificaiton cloud](authentication-national-cloud.md) pour obtenir une liste des URL du portail Azure et des points de terminaison de jeton de chaque cloud.

Documentation sur le cloud national :

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Allemagne](../../germany/index.yml)