---
title: Configurer des applications démon appelant des API web - Plateforme d’identités Microsoft | Azure
description: Découvrir comment configurer le code de votre application démon qui appelle des API web (configuration d’application)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0fd50f730c604ba1359218cf5268bd20e570d3c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962642"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Application démon appelant des API web - Configuration du code

Découvrez comment configurer le code de votre application démon qui appelle des API web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Bibliothèques MSAL prenant en charge des applications démon

Bibliothèques Microsoft prenant en charge les applications démon :

  Bibliothèque MSAL | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Les plateformes prises en charge pour la création d’une application démon sont .NET Framework et .NET Core (et non UWP, Xamarin.iOS et Xamarin.Android, car ces plateformes sont utilisées pour créer des applications clientes publiques)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Développement en cours, en préversion publique
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Développement en cours, en préversion publique

## <a name="configuration-of-the-authority"></a>Configuration de l’autorité

Étant donné que les applications démon n’utilisent pas d’autorisations déléguées, mais des autorisations d’application, leur *type de compte pris en charge* ne peut pas être du type *Comptes dans un annuaire organisationnel et comptes Microsoft personnels (par exemple, Skype, Xbox, Outlook.com)* . En effet, aucun administrateur de locataire n’octroie son consentement à l’application démon pour les comptes personnels Microsoft. Vous devez choisir des *comptes dans mon organisation* ou des *comptes dans une organisation*.

L’autorité spécifiée dans la configuration de l’application doit donc être locataire (en spécifiant un ID de locataire ou un nom de domaine associé à votre organisation).

Si vous êtes éditeur de logiciels indépendant et souhaitez fournir un outil multilocataire, vous pouvez utiliser `organizations`. Mais n’oubliez pas que vous devrez aussi expliquer à vos clients comment accorder le consentement administrateur. Pour plus d’informations, consultez [Demande de consentement d’un client entier](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant). De plus, il existe actuellement une limitation dans la bibliothèque MSAL : le paramètre `organizations` est autorisé uniquement quand les informations d’identification correspondent à un secret d’application (et non à un certificat).

## <a name="application-configuration-and-instantiation"></a>Instanciation et configuration d’application

Dans les bibliothèques MSAL, les informations d’identification de client (secret ou certificat) sont transmises en tant que paramètre de la construction d’application cliente confidentielle.

> [!IMPORTANT]
> Même si votre application est une application console s’exécutant en tant que service, si c’est une application démon, il devra s’agir d’une application cliente confidentielle.

### <a name="configuration-file"></a>Fichier de configuration

Le fichier de configuration définit les éléments suivants :

- autorité ou instance cloud et tenantId ;
- ClientID que vous avez obtenu de l’inscription de l’application ;
- secret client ou certificat.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[appsettings. json](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) de l’exemple de [démon de console .Net Core](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

Vous devez fournir un clientSecret ou un certificateName. Les deux paramètres sont exclusifs.

# <a name="pythontabpython"></a>[Python](#tab/python)

Lors de la création d’un client confidentiel avec des clés secrètes client, le fichier de configuration [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) de l’exemple [Démon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) se présente ainsi.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Lors de la création d’un client confidentiel avec des certificats, le fichier de configuration [parameters.json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) de l’exemple [Démon Python](https://github.com/Azure-Samples/ms-identity-python-daemon) se présente ainsi.

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Voici la classe utilisée dans les exemples de développement MSAL Java pour configurer les exemples : [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

---

### <a name="instantiation-of-the-msal-application"></a>Instanciation de l’application MSAL

Pour instancier l’application MSAL, vous devez effectuer les actions suivantes :

- ajouter, référencer ou importer le package MSAL (en fonction du langage).
- La construction est alors différente selon que vous utilisez des certificats ou des secrets clients (ou, en tant que scénario avancé, des assertions signées).

#### <a name="reference-the-package"></a>Référencer le package

Faites référence au package MSAL dans le code de votre application.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Ajoutez le package NuGet [Microsoft.IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) à votre application.
Dans MSAL.NET, l’application cliente confidentielle est représentée par l’interface `IConfidentialClientApplication`.
Utilisez l’espace de noms MSAL.NET dans le code source.

```CSharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-secrets"></a>Instancier l’application cliente confidentielle avec des secrets clients

Voici le code permettant d’instancier l’application cliente confidentielle avec un secret client :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-client-certificate"></a>Instancier l’application cliente confidentielle avec un certificat client

Voici le code permettant de créer une application avec un certificat :

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

Dans MSAL. Java il existe deux générateurs permettant d’instancier l’application cliente confidentielle avec des certificats :

```Java

InputStream pkcs12Certificate = ... ; /* containing PCKS12 formatted certificate*/
string certificatePassword = ... ;    /* contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

or

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario---instantiate-the-confidential-client-application-with-client-assertions"></a>Scénario avancé - Instancier l’application cliente confidentielle avec des assertions clientes

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

À la place d’un secret client ou d’un certificat, l’application cliente confidentielle peut aussi prouver son identité à l’aide d’assertions clientes.

MSAL.NET possède deux méthodes pour fournir des assertions signées à l’application cliente confidentielle :

- `.WithClientAssertion()`
- `.WithClientClaims()`

Lorsque vous utilisez `WithClientAssertion`, vous devez fournir un jeton JWT signé. Ce scénario avancé est détaillé dans [Assertions clientes](msal-net-client-assertions.md).

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Lorsque vous utilisez `WithClientClaims`, MSAL.NET génère une assertion signée contenant les revendications attendues par Azure AD en plus des revendications clientes supplémentaires que vous souhaitez envoyer.
Voici un extrait de code permettant de faire cela :

```CSharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

Encore une fois, pour plus d’informations, consultez [Assertions clientes](msal-net-client-assertions.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

Dans MSAL Python, vous pouvez fournir des revendications de client à l’aide des revendications qui seront signées par la clé privée de `ConfidentialClientApplication`.

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Pour plus d’informations, voir la documentation de référence de MSAL Python relative à [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java est en préversion publique. Les assertions signées ne sont pas encore prises en charge.

---

## <a name="next-steps"></a>Étapes suivantes

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Application démon conçue pour appeler des API web - acquisition d’un jeton](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Application démon conçue pour appeler des API web - acquisition d’un jeton](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Application démon conçue pour appeler des API web - acquisition d’un jeton](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
