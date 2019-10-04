---
title: Assertions client dans Microsoft Authentication Library pour .NET | Azure
description: En savoir plus sur la prise en charge des assertions client signées pour les applications client confidentielles dans Microsoft Authentication Library pour .NET (MSAL.NET).
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ea75499334f3f6eb2f5d3c15526067fcef4eb8
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442504"
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

### <a name="signed-assertions"></a>Assertions signées

Une assertion cliente signée prend la forme d’un JWT signé avec la charge utile contenant les revendications d’authentification requises mandatées par Azure AD, encodé en base64. Pour l’utiliser :

```CSharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Les revendications attendues par Azure AD sont :

Type de revendication | Valeur | Description
---------- | ---------- | ----------
aud | https://login.microsoftonline.com/{tenantId}/v2.0 | La revendication « AUD » (audience) identifie les destinataires auxquels le JWT est destiné (ici Azure AD) consultez [RFC 7519, section 4.1.3]
exp | Jeudi 27 juin 2019 à 15:04:17 GMT+0200 (Romance Daylight Time) | La revendication « exp » (délai d'expiration) indique le délai d'expiration à partir duquel le JWT ne doit PAS être accepté pour être traité. Consultez [RFC 7519, section 4.1.4]
iss | {ClientID} | La revendication « ISS » (émetteur) identifie le principal qui a émis le jeton JWT. Le traitement de cette revendication est spécifique à l’application. La valeur « ISS » est une chaîne qui respecte la casse et qui contient une valeur StringOrURI. [RFC 7519, section 4.1.1]
jti | (un guid) | La revendication « JTI » (ID JWT) fournit un identificateur unique pour le jeton JWT. La valeur de l’identificateur DOIT être assignée de manière à garantir qu’il y a une probabilité négligeable que la même valeur soit affectée par accident à un objet de données différent ; si l’application utilise plusieurs émetteurs, les collisions doivent également être évitées parmi les valeurs générées par différents émetteurs. La revendication « JTI » peut être utilisée pour empêcher la relecture du JWT. La valeur « JTI » est une chaîne qui respecte la casse. [RFC 7519, section 4.1.7]
nbf | Jeudi 27 juin 2019 à 14:54:17 GMT+0200 (Romance Daylight Time) | La revendication « nbf » (pas avant) indique le délai avant lequel le JWT ne doit PAS être accepté pour être traité. [RFC 7519, section 4.1.5]
sub | {ClientID} | La revendication « SUB » (objet) identifie l’objet du jeton JWT. Les revendications dans un jeton JWT sont normalement des instructions sur l’objet. La valeur de l’objet DOIT être étendue sur une valeur locale unique dans le contexte de l’émetteur ou être globalement unique. Consultez ensuite [RFC 7519, section 4.1.2]

L’exemple suivant montre comment créer ces revendications :

```CSharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = "https://login.microsoftonline.com/72f988bf-86f1-41af-hd4m-2d7cd011db47/v2.0";

      string ConfidentialClientID = "61dab2ba-145d-4b1b-8569-bf4b9aed5dhb" //client id
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

```CSharp
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

string GetAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
    X509Certificate2 certificate = ReadCertificate(config.CertificateName);

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
    string SignedAssertion = string.Concat(token, ".", signature);
    return SignedAssertion;
}
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` par défaut génère une assertion signée contenant les revendications attendues par Azure AD plus les revendications client supplémentaires que vous souhaitez envoyer. Voici un extrait de code sur la procédure à suivre.

```CSharp
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
