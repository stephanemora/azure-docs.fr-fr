---
title: Instancier une application de client confidentiel avec options (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: Découvrez comment instancier une application cliente confidentielle avec les options de configuration à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544081"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Instancier une application cliente confidentielle avec les options de configuration à l’aide de MSAL.NET

Cet article explique comment instancier un [application cliente confidentielle](msal-client-applications.md) à l’aide de Microsoft Authentication Library pour .NET (MSAL.NET).  L’application est instanciée avec les options de configuration définies dans un fichier de paramètres.

Avant d’initialiser une application, vous devez d’abord [inscrire](quickstart-register-app.md) il afin que votre application peut être intégrée à la plateforme Microsoft identity. Après l’inscription, vous devrez peut-être les informations suivantes (qui se trouve dans le portail Azure) :

- L’ID de client (une chaîne représentant un GUID)
- L’URL de fournisseur d’identité (l’instance nommée) et l’audience de connexion pour votre application. Ces deux paramètres sont collectivement regroupés sous l’autorité.
- ID de locataire si vous écrivez une ligne de l’application métier uniquement pour votre organisation (également nommée application à locataire unique).
- Le secret d’application (chaîne secrète client) ou le certificat (de type X509Certificate2) s’il s’agit d’une application client confidentiel.
- Pour les applications web et parfois pour les applications de client public (en particulier lorsque votre application doit utiliser un répartiteur), vous devez avoir également définir l’URI de redirection où le fournisseur d’identité contactera back votre application avec les jetons de sécurité.

## <a name="configure-the-application-from-the-config-file"></a>Configurer l’application à partir du fichier de configuration
Le nom des propriétés des options de MSAL.NET correspond au nom des propriétés de la `AzureADOptions` dans ASP.NET Core, par conséquent, vous n’avez pas besoin d’écrire de code de collage.

Configuration d’une application ASP.NET Core est décrite dans une *appsettings.json* fichier :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

À compter de MSAL.NET v3.x, vous pouvez configurer votre application cliente confidentielle à partir du fichier de configuration. Les classes liées à la configuration de l’application se trouvent dans le `Microsoft.Identity.Client.AppConfig` espace de noms.

Dans la classe où vous souhaitez configurer et instancier votre application, vous devez déclarer un `ConfidentialClientApplicationOptions` objet.  Lier la configuration lue dans la source (y compris le fichier appconfig.json) à l’instance des options d’application :

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Ainsi, le contenu de la section « AzureAD » de la *appsettings.json* fichier à lier aux propriétés correspondantes de la `ConfidentialClientApplicationOptions` objet.  Ensuite, générez un `ConfidentialClientApplication` objet :

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Ajouter la configuration du runtime
Dans une application cliente confidentielle, vous disposez généralement d’un cache par utilisateur. Par conséquent, vous devrez obtenir le cache associé à l’utilisateur et d’informer le Générateur d’applications que vous souhaitez l’utiliser. Dans la même façon, vous pouvez avoir un URI de redirection calculée de façon dynamique. Dans ce cas, le code est la suivante :

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

