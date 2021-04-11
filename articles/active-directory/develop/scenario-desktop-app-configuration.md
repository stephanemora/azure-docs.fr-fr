---
title: Configurer des applications de bureau appelant des API web | Azure
titleSuffix: Microsoft identity platform
description: Découvrir comment configurer le code d’une application de bureau qui appelle des API web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 27ee58a19191c6f8232a62b8251816784a98d373
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799052"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Application de bureau qui appelle des API web : Configuration de code

Maintenant que vous avez créé votre application, vous allez apprendre à configurer le code avec les coordonnées de l’application.

## <a name="microsoft-libraries-supporting-desktop-apps"></a>Bibliothèques Microsoft prenant en charge les applications de bureau

Les bibliothèques Microsoft suivantes prennent en charge les applications de bureau :

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="public-client-application"></a>Applications clientes publiques

Du point de vue du code, les applications de bureau sont des applications clientes publiques. La configuration est légèrement différente avec ou sans authentification interactive.

# <a name="net"></a>[.NET](#tab/dotnet)

Vous devez créer et manipuler `IPublicClientApplication` MSAL.NET.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivement par code

Le code suivant instancie une application cliente publique et connecte les utilisateurs dans le cloud public Microsoft Azure, avec un compte professionnel ou scolaire, ou un compte Microsoft personnel.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Si vous prévoyez d'utiliser l'authentification interactive ou le flux de code d'appareil, comme indiqué précédemment, utilisez le modificateur `.WithRedirectUri` :

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Utiliser des fichiers de configuration

Le code suivant instancie une application cliente publique à partir d'un objet de configuration, qui pourrait être renseigné par programmation ou lu à partir d'un fichier config.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuration plus élaborée

Vous pouvez développer la création d’une application en ajoutant un certain nombre de modificateurs. Par exemple, si vous souhaitez que votre application soit mutualisée dans un cloud national, tel que US Government comme illustré ici, vous pouvez écrire :

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET contient également un modificateur pour les services de fédération Active Directory (AD FS) 2019 :

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Enfin, si vous souhaitez acquérir des jetons pour un locataire Azure Active Directory (Azure AD) B2C, spécifiez votre client, comme indiqué dans l'extrait de code suivant :

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>En savoir plus

Pour en savoir plus sur la configuration d'une application de bureau MSAL.NET :

- Pour obtenir la liste de tous les modificateurs disponibles sur `PublicClientApplicationBuilder`, consultez [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) dans la documentation de référence.
- Pour obtenir la description de toutes les options exposées dans `PublicClientApplicationOptions`, consultez [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) dans la documentation de référence.

### <a name="complete-example-with-configuration-options"></a>Exemple complet avec options de configuration

Imaginez une application console .NET Core disposant du fichier config `appsettings.json` suivant :

```json
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

Vous avez peu de code à lire dans ce fichier via l'infrastructure de configuration .NET fournie :

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
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

  // Read the auth and graph endpoint configuration
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

Pour créer votre application, écrivez le code suivant :

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Avant l'appel à la méthode `.Build()`, vous pouvez remplacer votre configuration par des appels aux méthodes `.WithXXX`, comme indiqué précédemment.

# <a name="java"></a>[Java](#tab/java)

Voici la classe utilisée dans les exemples de développement MSAL Java pour configurer les échantillons : [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Le code suivant instancie une application cliente publique et connecte les utilisateurs dans le cloud public Microsoft Azure, avec un compte professionnel ou scolaire, ou un compte Microsoft personnel.

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

Vous pouvez développer la création d’une application en ajoutant un certain nombre de modificateurs. Par exemple, si vous souhaitez que votre application soit mutualisée dans un cloud national, tel que US Government comme illustré ici, vous pouvez écrire :

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

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Vous pouvez charger les paramètres de configuration à partir de nombreuses sources, telles qu’un fichier JSON ou des variables d’environnement. Un fichier *.env* est utilisé ci-dessous. 

```Text
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Info_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Chargez le fichier *.env* dans des variables d’environnement. Vous pouvez initier le nœud MSAL au minimum comme ci-dessous. Consultez les [options de configuration](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) disponibles.  

```JavaScript
const { PublicClientApplication } = require('@azure/msal-node');

const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

clientApplication = new PublicClientApplication(MSAL_CONFIG);
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant de ce scénario, [Acquérir un jeton pour l’application de bureau](scenario-desktop-acquire-token.md).