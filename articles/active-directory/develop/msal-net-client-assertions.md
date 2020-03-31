---
title: Assertions client (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: En savoir plus sur la prise en charge des assertions client signées pour les applications client confidentielles dans Microsoft Authentication Library pour .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/18/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8c97387bfd2a362d3bf5a6b8a3252242f061da31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050289"
---
# <a name="confidential-client-assertions"></a>Assertions client confidentielles

Afin de prouver leur identité, les applications clientes confidentielles échangent un secret avec Azure AD. Le secret peut être :
- Une clé secrète client (mot de passe de l’application).
- Un certificat utilisé pour générer une assertion signée contenant des revendications standards.

Ce secret peut également être une assertion signée directement.

MSAL.NET possède quatre méthodes pour fournir des informations d’identification ou des assertions à l’application cliente confidentielle :
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Même s’il est possible d’utiliser l’API `WithClientAssertion()` pour acquérir des jetons pour le client confidentiel, nous vous déconseillons de l’utiliser par défaut, car elle est plus avancée et est conçue pour gérer des scénarios très spécifiques qui ne sont pas courants. L’utilisation de l’API `.WithCertificate()` permet à MSAL.NET de gérer cela pour vous. Cette API vous donne la capacité de personnaliser votre demande d’authentification si nécessaire, mais l’assertion par défaut créée par `.WithCertificate()` suffit dans la plupart des scénarios d’authentification. Cette API peut aussi être utilisée comme solution de contournement dans certains scénarios où MSAL.NET ne parvient pas à effectuer l’opération de signature en interne.

### <a name="signed-assertions"></a>Assertions signées

Une assertion cliente signée prend la forme d’un JWT signé avec la charge utile contenant les revendications d’authentification requises mandatées par Azure AD, encodé en base64. Pour l’utiliser :

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Les revendications attendues par Azure AD sont :

Type de revendication | Valeur | Description
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | La revendication « AUD » (audience) identifie les destinataires auxquels le JWT est destiné (ici Azure AD) consultez [RFC 7519, section 4.1.3]
exp | Jeudi 27 juin 2019 à 15:04:17 GMT+0200 (Romance Daylight Time) | La revendication « exp » (délai d'expiration) indique le délai d'expiration à partir duquel le JWT ne doit PAS être accepté pour être traité. Consultez [RFC 7519, section 4.1.4]
iss | {ClientID} | La revendication « ISS » (émetteur) identifie le principal qui a émis le jeton JWT. Le traitement de cette revendication est spécifique à l’application. La valeur « ISS » est une chaîne qui respecte la casse et qui contient une valeur StringOrURI. [RFC 7519, section 4.1.1]
jti | (un guid) | La revendication « JTI » (ID JWT) fournit un identificateur unique pour le jeton JWT. La valeur de l’identificateur DOIT être assignée de manière à garantir qu’il y a une probabilité négligeable que la même valeur soit affectée par accident à un objet de données différent ; si l’application utilise plusieurs émetteurs, les collisions doivent également être évitées parmi les valeurs générées par différents émetteurs. La revendication « JTI » peut être utilisée pour empêcher la relecture du JWT. La valeur « JTI » est une chaîne qui respecte la casse. [RFC 7519, section 4.1.7]
nbf | Jeudi 27 juin 2019 à 14:54:17 GMT+0200 (Romance Daylight Time) | La revendication « nbf » (pas avant) indique le délai avant lequel le JWT ne doit PAS être accepté pour être traité. [RFC 7519, section 4.1.5]
sub | {ClientID} | La revendication « SUB » (objet) identifie l’objet du jeton JWT. Les revendications dans un jeton JWT sont normalement des instructions sur l’objet. La valeur de l’objet DOIT être étendue sur une valeur locale unique dans le contexte de l’émetteur ou être globalement unique. Consultez ensuite [RFC 7519, section 4.1.2]

L’exemple suivant montre comment créer ces revendications :

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Voici comment créer une assertion de client signée :

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Autre méthode

Vous avez également la possibilité d’utiliser [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) pour créer l’assertion pour vous. Le code sera plus clair, comme illustré dans l’exemple ci-dessous :

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Une fois que vous avez votre assertion cliente signée, vous pouvez l’utiliser avec les API MSAL, comme montré ci-dessous.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` par défaut génère une assertion signée contenant les revendications attendues par Azure AD plus les revendications client supplémentaires que vous souhaitez envoyer. Voici un extrait de code sur la procédure à suivre.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Si l’une des revendications du dictionnaire que vous transmettez est identique à l’une des revendications obligatoires, la valeur de la revendication supplémentaire sera prise en compte. Elle remplacera les revendications calculées par MSAL.NET.

Si vous souhaitez fournir vos propres revendications, y compris les revendications obligatoires attendues par Azure AD, transmettez `false` pour le paramètre `mergeWithDefaultClaims`.
