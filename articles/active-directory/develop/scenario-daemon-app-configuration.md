---
title: Démon application appeler des API web (configuration de l’application) - plateforme d’identité Microsoft
description: Découvrez comment créer une application démon par appels web API (configuration de l’application)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: d8d377db827a6548c380128624c21f4ae7896aff
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075324"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Application démon par appels web API - configuration du code

Découvrez comment configurer le code de votre application démon appelle des API web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Applications de démon de prise en charge les bibliothèques MSAL

Les bibliothèques Microsoft prenant en charge les applications de démon sont :

  Bibliothèque MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Plateformes prises en charge pour créer une application démon sont les plateformes .NET Framework et .NET Core (pas UWP, Xamarin.iOS et Xamarin.Android en tant que ces plateformes sont utilisées pour créer des applications de client public)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Développement en cours, en version préliminaire publique
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Développement en cours, en version préliminaire publique

## <a name="configuration-of-the-authority"></a>Configuration de l’autorité

Étant donné que les applications de démon n’utilisent pas les autorisations déléguées, mais les autorisations d’application, leur *pris en charge le type de compte* ne peut pas être *comptes dans n’importe quel répertoire Professionnel et personnel Microsoft comptes () par exemple, Skype, Xbox, Outlook.com)*. En effet, il n’existe aucun administrateur locataire à donner son consentement à l’application démon pour les comptes Microsoft personnels. Vous devrez choisir *des comptes de mon organisation* ou *comptes dans n’importe quelle organisation*.

L’autorité spécifiée dans la configuration de l’application doit donc être locataire-ed (en spécifiant un ID de locataire ou un nom de domaine associé à votre organisation). Si vous êtes éditeur de logiciels indépendant et que vous souhaitez fournir un outil d’architecture mutualisé, vous pouvez utiliser `organizations`. Mais n’oubliez pas que vous devrez également expliquer à vos clients comment accorder le consentement de l’administrateur. Consultez [demande de consentement d’un client entier](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) pour plus d’informations

## <a name="application-configuration-and-instantiation"></a>Instanciation et configuration de l’application

Dans les bibliothèques MSAL, les informations d’identification de client (clé secrète ou certificat) sont passées en tant que paramètre de la construction d’application client confidentiel.

> [!IMPORTANT]
> Même si votre application est une application console en cours d’exécution en tant que service, si c’est une application démon il devra être une application cliente confidentielle.

### <a name="msalnet"></a>MSAL.NET

Ajouter le [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) package NuGet à votre application.

Utiliser l’espace de noms MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

Le démon sera présentée par un `IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Voici le code pour créer une application avec un secret d’application :

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Voici le code pour générer une application avec un certificat :

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Application démon - acquisition de jetons pour l’application](./scenario-daemon-acquire-token.md)