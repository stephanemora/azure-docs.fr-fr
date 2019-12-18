---
title: Configurer une application de bureau appelant des API web - Plateforme d’identités Microsoft | Azure
description: Découvrir comment configurer le code d’une application de bureau qui appelle des API web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d31a70ebc63a5e9a16e0da00623bd5855f0a7d1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920257"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Application de bureau appelant des API web - Configuration du code

Maintenant que vous avez créé votre application, vous allez apprendre à configurer le code avec les coordonnées de l’application.

## <a name="msal-libraries"></a>Bibliothèques MSAL

Bibliothèques Microsoft prenant en charge les applications de bureau :

  Bibliothèque MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Prend en charge la création d’une application de bureau sur plusieurs plateformes : Linux, Windows et macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Prend en charge la création d’une application de bureau sur plusieurs plateformes. Développement en cours, en préversion publique
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Prend en charge la création d’une application de bureau sur plusieurs plateformes. Développement en cours, en préversion publique
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Prend en charge les applications de bureau sur macOS uniquement.

## <a name="public-client-application"></a>Applications clientes publiques

Du point de vue du code, les applications de bureau sont des applications clientes publiques. La configuration est légèrement différente avec ou sans authentification interactive.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Vous devez créer et manipuler `IPublicClientApplication` MSAL.NET.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivement par code

Le code suivant instancie une application cliente publique en connectant les utilisateurs dans le cloud public Microsoft Azure, avec un compte professionnel et scolaire ou un compte Microsoft personnel.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Si vous prévoyez d’utiliser l’authentification interactive ou le flux de code de l’appareil, comme vu ci-dessus, préférez utiliser le modificateur `.WithRedirectUri` :

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Utilisation des fichiers de configuration

Le code suivant instancie une application cliente publique à partir d’un objet de configuration, qui pourrait être renseigné par programmation ou lu à partir d’un fichier config.

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuration plus élaborée

Vous pouvez développer la création d’une application en ajoutant un certain nombre de modificateurs. Par exemple, si vous souhaitez que votre application soit mutualisée dans un cloud national (US Government, en l’occurrence), vous pouvez écrire :

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
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

Pour en savoir plus sur la configuration d’une application de bureau MSAL.NET :

- Pour obtenir la liste de tous les modificateurs disponibles sur `PublicClientApplicationBuilder`, consultez la documentation de référence [publicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Pour la description de toutes les options exposées dans `PublicClientApplicationOptions`, consultez [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), dans la documentation de référence.

### <a name="complete-example-with-configuration-options"></a>Exemple complet avec les options de configuration

Imaginez une application console .NET Core disposant du fichier config `appsettings.json` suivant :

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

Vous avez besoin de peu de code pour lire ce fichier via l’infrastructure de configuration .NET fournie ;

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

Maintenant, pour créer votre application, il vous suffit d’écrire le code suivant :

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

et avant l’appel à la méthode `.Build()`, vous pouvez remplacer votre configuration avec des appels aux méthodes `.WithXXX`, comme vu précédemment.

# <a name="javatabjava"></a>[Java](#tab/java)

Voici la classe utilisée dans les exemples de développement MSAL Java pour configurer les exemples : [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

Le code suivant instancie une application cliente publique en connectant les utilisateurs dans le cloud public Microsoft Azure, avec un compte professionnel et scolaire ou un compte Microsoft personnel.

### <a name="quick-configuration"></a>Configuration rapide

Objective-C :

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift :
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configuration plus élaborée

Vous pouvez développer la création d’une application en ajoutant un certain nombre de modificateurs. Par exemple, si vous souhaitez que votre application soit mutualisée dans un cloud national (US Government, en l’occurrence), vous pouvez écrire :

Objective-C :

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

Swift :

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Acquisition d’un jeton une application de bureau](scenario-desktop-acquire-token.md)
