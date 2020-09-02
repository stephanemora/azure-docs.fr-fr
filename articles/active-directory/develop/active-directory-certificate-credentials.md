---
title: Informations d’identification de certificat de la Plateforme d’identités Microsoft
titleSuffix: Microsoft identity platform
description: Cet article traite de l’inscription et de l’utilisation des informations d’identification de certificat pour l’authentification d’application.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6330621aac78d5e9df52f2cd3ad9c3968bb0120d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853383"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Informations d’identification de certificat d’authentification d’application de la Plateforme d’identités Microsoft

La plateforme d’identités Microsoft permet à une application d’utiliser ses propres informations d’identification pour l’authentification, par exemple, le [flux d’octroi d’informations d’identification du client](v2-oauth2-client-creds-grant-flow.md) OAuth 2.0 et le [flux On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) (OBO).

Parmi les types d’informations d’identification qu’une application peut utiliser pour l’authentification figure l’assertion [JSON Web Token](./security-tokens.md#json-web-tokens-jwts-and-claims) (JWT) signée avec un certificat dont est propriétaire l’application.

## <a name="assertion-format"></a>Format d’assertion

Pour calculer l’assertion, vous pouvez utiliser l’une des nombreuses bibliothèques JWT dans la langue de votre choix. Les informations sont transmises par le jeton dans son [En-tête ](#header), ses [Revendications](#claims-payload) et sa [Signature](#signature).

### <a name="header"></a>En-tête

| Paramètre |  Remarque |
| --- | --- |
| `alg` | Doit être **RS256** |
| `typ` | Doit être **JWT** |
| `x5t` | Le code de hachage du certificat X.509 (également connu sous le nom d’*empreinte* de certificat SHA-1) est une représentation hexadécimale encodée en tant que valeur de chaîne base64. Par exemple, avec un hachage de certificat X.509 de `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (hexadécimal), la revendication `x5t` serait `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64). |

### <a name="claims-payload"></a>Revendications (charge utile)

| Paramètre |  Notes |
| --- | --- |
| `aud` | Audience: Doit être `https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | Date d’expiration : La date d’expiration du jeton. L’heure est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’à l’expiration du jeton. Nous vous recommandons d’utiliser un délai d’expiration court, de 10 minutes à une heure.|
| `iss` | Émetteur : Doit être le paramètre client_id (*ID de l’application (client)* du service client) |
| `jti` | GUID : L’ID JWT |
| `nbf` | Pas avant : La date avant laquelle le jeton ne peut pas être utilisé. L’heure est représentée en nombre de secondes à partir du 1er janvier 1970 (1970-01-01T0:0:0Z) UTC jusqu’au moment de la création de l’assertion. |
| `sub` | Objet : Pour `iss`, doit être le paramètre client_id (*ID de l’application (client)* du service client) |

### <a name="signature"></a>Signature

La signature est calculée en appliquant le certificat, conformément à la [spécification RFC7519 sur les jetons Web JSON](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Exemple d’une assertion JWT décodée

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Exemple d’une assertion JWT encodée

La chaîne suivante est un exemple d’assertion encodée. Si vous regardez attentivement, vous remarquerez les trois sections séparées par des points (`.`) :

* La première section encode l’*en-tête*
* La deuxième section encode les *revendications* (charge utile)
* La dernière section est la *signature* calculée avec les certificats à partir du contenu des deux premières sections

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Inscrire votre certificat auprès de la Plateforme d’identités Microsoft

Vous pouvez associer les informations d’identification du certificat à l’application cliente dans la Plateforme d’identités Microsoft via le portail Azure en utilisant l’une des méthodes suivantes :

### <a name="uploading-the-certificate-file"></a>Chargement du fichier de certificat

Dans l’inscription d’application Azure pour l’application cliente :
1. Cliquez sur **Certificats et secrets**.
2. Cliquez sur **Charger un certificat** et sélectionnez le fichier de certificat à charger.
3. Cliquez sur **Add**.
  Une fois le certificat chargé, les valeurs d'empreinte numérique, de date de début et d’expiration s'affichent.

### <a name="updating-the-application-manifest"></a>Mise à jour du manifeste d’application

Sur la base de votre certificat, vous devez calculer :

- `$base64Thumbprint` -Valeur encodée en base64 du code de hachage du certificat
- `$base64Value` -Valeur encodée en base64 des données brutes du certificat

Vous devez également fournir un GUID pour identifier la clé dans le manifeste de l’application (`$keyId`).

Dans l’inscription d’application Azure pour l’application cliente :
1. Sélectionnez **Manifeste** pour ouvrir le manifeste d’application.
2. Remplacez la propriété *keyCredentials* par les nouvelles informations de votre certificat, en utilisant le schéma suivant.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Enregistrez les modifications du manifeste de l’application, puis chargez-le dans la Plateforme d’identités Microsoft.

   La propriété `keyCredentials` peut avoir plusieurs valeurs. Vous pouvez donc charger plusieurs certificats pour une gestion plus élaborée des clés.

## <a name="next-steps"></a>Étapes suivantes

L’exemple de code [Application console de démon .NET Core utilisant la plateforme d’identité Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) sur GitHub montre comment une application utilise ses propres informations d’identification pour l’authentification. Il montre également comment vous pouvez [créer un certificat auto-signé](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) à l’aide de la cmdlet PowerShell `New-SelfSignedCertificate`. Vous pouvez également utiliser les [scripts de création d’application](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) dans le référentiel d’échantillons pour créer des certificats, calculer l’empreinte, etc.
