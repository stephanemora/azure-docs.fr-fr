---
title: Instancier une application de client public avec des options (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: Découvrez comment instancier une application cliente publique avec les options de configuration à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.openlocfilehash: 125bbf9aed54fb00f039aeffddd5cc1aad3360a6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544397"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instancier une application cliente publique avec les options de configuration à l’aide de MSAL.NET

Cet article explique comment instancier un [application cliente publique](msal-client-applications.md) à l’aide de Microsoft Authentication Library pour .NET (MSAL.NET).  L’application est instanciée avec les options de configuration définies dans un fichier de paramètres.

Avant d’initialiser une application, vous devez d’abord [inscrire](quickstart-register-app.md) il afin que votre application peut être intégrée à la plateforme Microsoft identity. Après l’inscription, vous devrez peut-être les informations suivantes (qui se trouve dans le portail Azure) :

- L’ID de client (une chaîne représentant un GUID)
- L’URL de fournisseur d’identité (l’instance nommée) et l’audience de connexion pour votre application. Ces deux paramètres sont collectivement regroupés sous l’autorité.
- ID de locataire si vous écrivez une ligne de l’application métier uniquement pour votre organisation (également nommée application à locataire unique).
- Pour les applications web et parfois pour les applications de client public (en particulier lorsque votre application doit utiliser un répartiteur), vous devez avoir également définir l’URI de redirection où le fournisseur d’identité contactera back votre application avec les jetons de sécurité.


Une application console .NET Core peut avoir les éléments suivants *appsettings.json* fichier de configuration :

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

Le code suivant lit ce fichier à l’aide de l’infrastructure de configuration .NET :

```csharp
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
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Le code suivant crée votre application, à l’aide de la configuration du fichier de paramètres :

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

