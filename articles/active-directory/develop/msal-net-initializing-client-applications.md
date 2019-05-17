---
title: Initialiser des applications clientes (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: En savoir plus sur l’initialisation de client public et les applications de client confidentiel à l’aide de la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f22ff41e380a16af2aa45df9a61eefbf293ff83
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544316"
---
# <a name="initialize-client-applications-using-msalnet"></a>Initialiser des applications clientes à l’aide de MSAL.NET
Cet article décrit l’initialisation de client public et les applications de client confidentiel à l’aide de Microsoft Authentication Library pour .NET (MSAL.NET).  Pour en savoir plus sur les types d’applications clientes et les options de configuration d’application, consultez le [vue d’ensemble](msal-client-applications.md).

Avec MSAL.NET 3.x, la méthode recommandée pour instancier une application est à l’aide de générateurs d’applications : `PublicClientApplicationBuilder` et `ConfidentialClientApplicationBuilder`. Elles offrent un mécanisme puissant pour configurer l’application à partir du code, ou à partir d’un fichier de configuration ou même à l’association de ces deux approches.

## <a name="prerequisites"></a>Conditions préalables
Avant d’initialiser une application, vous devez d’abord [Inscrivez-le](quickstart-register-app.md) afin que votre application peut être intégrée à la plateforme Microsoft identity.  Après l’inscription, vous devrez peut-être les informations suivantes (qui se trouve dans le portail Azure) :

- L’ID de client (une chaîne représentant un GUID)
- L’URL de fournisseur d’identité (l’instance nommée) et l’audience de connexion pour votre application. Ces deux paramètres sont collectivement regroupés sous l’autorité.
- ID de locataire si vous écrivez une ligne de l’application métier uniquement pour votre organisation (également nommée application à locataire unique).
- Le secret d’application (chaîne secrète client) ou le certificat (de type X509Certificate2) s’il s’agit d’une application client confidentiel.
- Pour les applications web et parfois pour les applications de client public (en particulier lorsque votre application doit utiliser un répartiteur), vous devez avoir également définir l’URI de redirection où le fournisseur d’identité contactera back votre application avec les jetons de sécurité.

## <a name="ways-to-initialize-applications"></a>Comment initialiser des applications
Il existe différentes manières d’instancier les applications clientes.

### <a name="initializing-a-public-client-application-from-code"></a>L’initialisation d’une application de client public à partir du code

Le code suivant instancie une application cliente publique, les utilisateurs de signature dans le cloud public Microsoft Azure, avec leur travail et les comptes scolaires ou leurs comptes Microsoft personnels.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>L’initialisation d’une application cliente confidentielle à partir du code

Dans la même façon, le code suivant instancie une application confidentielle (une application Web est situé dans `https://myapp.azurewebsites.net`) gestion des jetons à partir des utilisateurs dans le cloud public Microsoft Azure, avec leur travail et les comptes scolaires ou leurs comptes Microsoft personnels. L’application est identifiée par le fournisseur d’identité en partageant une clé secrète client :

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Comme vous le savez peut-être, en production, et non à l’aide d’une clé secrète client, vous souhaiterez peut-être partager avec Azure AD un certificat. Le code serait alors les éléments suivants :

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>L’initialisation d’une application de client public à partir des options de configuration

Le code suivant instancie une application cliente publique à partir d’un objet de configuration, ce qui pourrait être renseignés par programmation ou lire à partir d’un fichier de configuration :

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>L’initialisation d’une application cliente confidentielle à partir des options de configuration

Le même type de modèle s’applique aux applications de client confidentiel. Vous pouvez également ajouter d’autres paramètres à l’aide de `.WithXXX` modificateurs (ici un certificat).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificateurs de générateur de rapports

Dans les extraits de code à l’aide de générateurs d’applications, un nombre de `.With` méthodes peuvent être appliquées en tant que modificateurs (par exemple, `.WithCertificate` et `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificateurs communes aux applications clientes publiques et confidentielles

Les modificateurs que vous pouvez définir sur un client public ou le Générateur d’applications client confidentiel sont :

|Paramètre | Description |
|--------- | --------- |
|`.WithAuthority()` 7 remplacements | Définit l’autorité de valeur par défaut d’application à une autorité Azure AD, avec la possibilité de choisir le Cloud Azure, l’audience, le locataire (locataire ID nom ou de domaine), ou fournir directement l’URI d’autorité.|
|`.WithAdfsAuthority(string)` | Définit l’application par défaut autorité une autorité d’ADFS.|
|`.WithB2CAuthority(string)` | Définit l’application par défaut autorité une autorité Azure AD B2C.|
|`.WithClientId(string)` | Remplace l’ID client.|
|`.WithComponent(string)` | Définit le nom de la bibliothèque à l’aide de MSAL.NET (pour des raisons de télémétrie). |
|`.WithDebugLoggingCallback()` | Si elle est appelée, l’application appelle `Debug.Write` simplement l’activation suivis de débogage. Consultez [journalisation](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) pour plus d’informations.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Définissez les paramètres de requête supplémentaire de niveau application qui seront envoyés dans toutes les demandes d’authentification. Il s’agit substituable à chaque niveau de la méthode d’acquisition de jeton (avec le même `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Active les avancées des scénarios tels que la configuration d’un proxy HTTP, ou forcer MSAL à utiliser un HttpClient particulier (par exemple ASP.NET Core web apps/API).|
|`.WithLogging()` | Si elle est appelée, l’application appelle un rappel avec suivis de débogage. Consultez [journalisation](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) pour plus d’informations.|
|`.WithRedirectUri(string redirectUri)` | Remplace l’URI de redirection par défaut. Dans le cas des applications de client public, cela sera utile pour les scénarios impliquant le service broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Définit le délégué utilisé pour envoyer la télémétrie.|
|`.WithTenantId(string tenantId)` | Remplace l’ID de locataire, ou la description de locataire.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificateurs spécifiques aux applications de Xamarin.iOS

Les modificateurs que vous pouvez définir sur un générateur d’applications client public sur Xamarin.iOS sont :

|Paramètre | Description |
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS uniquement**: Définit le groupe de sécurité de porte-clés iOS (pour la persistance du cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificateurs spécifiques aux applications de client confidentiel

Les modificateurs que vous pouvez définir sur un générateur d’applications client confidentiel sont :

|Paramètre | Description |
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Définit le certificat identifiant l’application avec Azure AD.|
|`.WithClientSecret(string clientSecret)` | Définit la clé secrète client (mot de passe) identifiant l’application avec Azure AD.|

Ces modificateurs s’excluent mutuellement. Si vous fournissez à la fois, MSAL lève une exception explicite.

### <a name="example-of-usage-of-modifiers"></a>Exemple d’utilisation des modificateurs

Supposons que votre application est une application line of business, qui est uniquement pour votre organisation.  Vous pouvez écrire :

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Où elle devient intéressant est que la programmation des clouds nationaux a désormais simplifiée. Si vous souhaitez que votre application pour une application mutualisée dans un cloud national, vous pouvez écrire, par exemple :

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Il existe également un remplacement pour ADFS (ADFS 2019 est actuellement pas pris en charge) :
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Enfin, si vous êtes un développeur d’Azure AD B2C, vous pouvez spécifier votre client comme suit :

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
