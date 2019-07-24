---
title: Application démon conçue pour appeler des API web (configuration d’application) - Plateforme d’identités Microsoft
description: Découvrez comment générer une application démon qui appelle des API web (configuration d’application)
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
ms.openlocfilehash: fd2da6baecdce3ab85a45347f27f573bf814445d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055750"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Application démon appelant des API web - Configuration du code

Découvrez comment configurer le code de votre application démon qui appelle des API web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Bibliothèques MSAL prenant en charge des applications démon

Bibliothèques Microsoft prenant en charge les applications démon :

  Bibliothèque MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Les plateformes prises en charge pour la création d’une application démon sont .NET Framework et .NET Core (et non UWP, Xamarin.iOS et Xamarin.Android, car ces plateformes sont utilisées pour créer des applications clientes publiques)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Développement en cours, en préversion publique
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Développement en cours, en préversion publique

## <a name="configuration-of-the-authority"></a>Configuration de l’autorité

Étant donné que les applications démon n’utilisent pas d’autorisations déléguées, mais des autorisations d’application, leur *type de compte pris en charge* ne peut pas être du type *Comptes d’un répertoire organisationnel ni comptes Microsoft personnels (par exemple, Skype, Xbox, Outlook.com)* . En effet, aucun administrateur de locataire n’octroie son consentement à l’application démon pour les comptes Microsoft personnels. Vous devez choisir des *comptes dans mon organisation* ou des *comptes dans une organisation*.

L’autorité spécifiée dans la configuration de l’application doit donc être locataire (en spécifiant un ID de locataire ou un nom de domaine associé à votre organisation).

Si vous êtes éditeur de logiciels indépendant et souhaitez fournir un outil mutualisé, vous pouvez utiliser `organizations`. Mais n’oubliez pas que vous devrez également expliquer à vos clients comment accorder le consentement administrateur. Pour plus d’informations, consultez [Demande de consentement d’un client entier](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). De plus, il existe actuellement une limitation dans la bibliothèque MSAL qui fait que le paramètre `organizations` est autorisé uniquement lorsque les informations d’identification du client sont un secret d’application (et non un certificat). Voir [MSAL.NET bogue n° 891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Instanciation et configuration d’application

Dans les bibliothèques MSAL, les informations d’identification de client (secret ou certificat) sont transmises en tant que paramètre de la construction d’application cliente confidentielle.

> [!IMPORTANT]
> Même si votre application est une application console s’exécutant en tant que service, si c’est une application démon, il devra s’agir d’une application cliente confidentielle.

### <a name="msalnet"></a>MSAL.NET

Ajoutez le package NuGet [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) à votre application.

Utiliser l’espace de noms MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

L’application démon est présentée par un paramètre `IConfidentialClientApplication`.

```CSharp
IConfidentialClientApplication app;
```

Voici le code permettant de créer une application avec un secret d’application :

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Voici le code permettant de créer une application avec un certificat :

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
> [Application démon conçue pour appeler des API web - acquisition d’un jeton](./scenario-daemon-acquire-token.md)