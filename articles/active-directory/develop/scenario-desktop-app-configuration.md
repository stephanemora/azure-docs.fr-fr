---
title: Application de bureau que les appels web API (configuration de code) - plateforme d’identité Microsoft
description: Découvrez comment créer une application de bureau que les appels de web API (configuration de code de l’application)
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
ms.date: 05/o7/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c52f6fc66187d961dc93089a9f81f6de4d67fe41
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075939"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Application de bureau que les appels de web API - configuration du code

Maintenant que vous avez créé votre application, vous allez apprendre à configurer le code avec les coordonnées de l’application.

## <a name="msal-libraries"></a>Bibliothèques MSAL

La bibliothèque MSAL uniquement prise en charge aujourd'hui des applications de bureau est MSAL.NET

## <a name="public-client-application"></a>Application cliente publique

Du point de vue du code, applications de bureau sont des applications de client public, et c’est pourquoi vous allez créer et manipuler MSAL.NET `IPublicClientApplication`. Nouveau choses sera un peu différente que vous utilisez l’authentification interactive ou non.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivement par le code

Le code suivant instancie une application cliente publique, les utilisateurs de signature dans le cloud public Microsoft Azure, avec leur travail et les comptes scolaires ou leurs comptes Microsoft personnels.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Si vous envisagez d’utiliser l’authentification interactive, comme indiqué ci-dessus, vous souhaitez utiliser le `.WithRedirectUri` modificateur :

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri(PublicClientApplicationBuilder.DefaultInteractiveDesktopRedirectUri)
         .Build();
```

### <a name="using-configuration-files"></a>À l’aide de fichiers de configuration

Le code suivant instancie une application cliente publique à partir d’un objet de configuration, ce qui pourrait être renseignés par programmation ou lire à partir d’un fichier de configuration

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="more-elaborated-configuration"></a>Configuration plus élaborée

Vous pouvez développer l’application de création en ajoutant un numéro de modificateurs. Par exemple, si vous souhaitez que votre application pour une application mutualisée dans un cloud national (ici US Government), vous pouvez écrire :

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET contient également un modificateur pour ADFS 2019 :

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Enfin, si vous souhaitez acquérir des jetons pour un locataire Azure AD B2C, vous pouvez spécifier votre client, comme indiqué dans l’extrait de code suivant :

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>En savoir plus

Pour en savoir plus sur la façon de configurer une application de bureau MSAL.NET :

- Pour obtenir la liste de tous les modificateurs disponibles sur `PublicClientApplicationBuilder`, consultez la documentation de référence [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.publicclientapplicationbuilder?view=azure-dotnet-preview#methods)
- Pour la description de toutes les options exposées dans `PublicClientApplicationOptions` consultez [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.publicclientapplicationoptions?view=azure-dotnet-preview), dans la documentation de référence

## <a name="complete-example-with-configuration-options"></a>Exemple complet avec les Options de configuration

Imaginez une application console .NET Core suivantes `appsettings.json` fichier de configuration :

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Vous avez peu de code pour lire ce fichier à l’aide de .NET fourni l’infrastructure de configuration ;

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint config
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Maintenant, pour créer votre application, vous allez suffit d’écrire le code suivant :

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

et avant l’appel à la `.Build()` (méthode), vous pouvez remplacer votre configuration avec les appels à `.WithXXX` méthodes comme vu précédemment.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [L’acquisition d’un jeton pour une application de bureau](scenario-desktop-acquire-token.md)
