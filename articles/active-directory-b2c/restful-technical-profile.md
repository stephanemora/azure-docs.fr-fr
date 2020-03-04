---
title: Définir un profil technique RESTful dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définir un profil technique RESTful dans une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/24/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 80298ca4df01a93730fc831fc495b3123ead5f97
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585677"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique RESTful dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) prend en charge votre propre service RESTful. Azure Active Directory B2C envoie des données au service RESTful dans une collection de revendications d’entrée et reçoit des données en retour dans une collection de revendications de sortie. Avec l’intégration de service RESTful, vous pouvez :

- **Valider les données d’entrée utilisateur** : empêche la persistance de données mal formées AAD B2C. Si la valeur de l’utilisateur n’est pas valide, votre service RESTful retourne un message d’erreur qui demande à l’utilisateur de fournir une entrée. Par exemple, vous pouvez vérifier que l’adresse e-mail fournie par l’utilisateur existe dans la base de données de clients.
- **Remplacer des revendications d’entrée** : permet de remettre en forme les valeurs de revendications d’entrée. Par exemple, si un utilisateur entre le prénom entier en lettres minuscules ou majuscules, vous pouvez modifier sa mise en forme en utilisant une majuscule uniquement pour la première lettre.
- **Enrichir des données utilisateur** : permet d’approfondir l’intégration avec des applications métier d’entreprise. Par exemple, votre service RESTful peut recevoir l’adresse e-mail de l’utilisateur, interroger la base de données de clients et retourner le numéro de fidélité de l’utilisateur à Azure AD B2C. Les revendications retournées peuvent être stockées, évaluées dans les étapes d’orchestration suivantes, ou incluses dans le jeton d’accès.
- **Exécuter une logique métier personnalisée** : permet d’envoyer des notifications Push, de mettre à jour des bases de données d’entreprise, d’exécuter un processus de migration utilisateur, de gérer des autorisations, d’auditer des bases de données, et d’effectuer d’autres actions.

Votre stratégie peut envoyer des revendications d’entrée à votre API REST. L’API REST peut également retourner des revendications de sortie que vous pouvez utiliser ultérieurement dans votre stratégie, ou déclencher un message d’erreur. Vous pouvez concevoir l’intégration aux services RESTful comme suit :

- **Profil technique de validation** : un profil technique de validation appelle le service RESTful. Le profil technique de validation valide les données fournies par l’utilisateur avant la poursuite du parcours de celui-ci. Avec le profil technique de validation, un message d’erreur est affiché sur une page déclarée automatiquement, et retourné dans des revendications de sortie.
- **Échange de revendications** : un appel est effectué au service RESTful via une étape d’orchestration. Dans ce scénario, il n’existe pas d’interface utilisateur pour afficher le message d’erreur. Si votre API REST retourne une erreur, l’utilisateur est redirigé vers l’application de la partie de confiance avec le message d’erreur.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom qualifié complet d’Assembly de gestionnaire de protocole utilisé par Azure AD B2C : `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

L’exemple suivant montre un profil technique RESTful :

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Revendications d’entrée

L’élément **InputClaims** contient une liste de revendications à envoyer à l’API REST. Vous pouvez également mapper le nom de votre revendication au nom défini dans l’API REST. L’exemple suivant montre le mappage entre votre stratégie et l’API REST. La revendication **givenName** est envoyée à l’API REST en tant que **firstName**, tandis que la revendication **surname** est envoyée en tant que **lastName**. La revendication **email** est définie telle quelle.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles avant l’envoi à l’API REST.

## <a name="send-a-json-payload"></a>Envoyer une charge utile JSON

Le profil technique de l’API REST vous permet d’envoyer une charge utile JSON complexe à un point de terminaison.

Pour envoyer une charge utile JSON complexe :

1. Créez votre charge utile JSON avec la transformation de revendications [GenerateJson](json-transformations.md).
1. Dans le profil technique API REST :
    1. Ajoutez une transformation de revendications d’entrée avec une référence à la transformation de revendications `GenerateJson`.
    1. Affectez à l’option de métadonnées `SendClaimsIn` la valeur `body`
    1. Donnez à l’option de métadonnées `ClaimUsedForRequestPayload` le nom de la revendication contenant la charge utile JSON.
    1. Dans la revendication d’entrée, ajoutez une référence à la revendication d’entrée contenant la charge utile JSON.

L’exemple suivant `TechnicalProfile` envoie un e-mail de vérification à l’aide d’un service de messagerie tiers (dans ce cas, SendGrid).

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications retournée par l’API REST. Il se peut que vous deviez mapper le nom de la revendication définie dans votre stratégie au nom défini dans l’API REST. Vous pouvez également inclure des revendications qui ne sont pas retournées par le fournisseur d’identité de l’API REST, pour autant que vous définissiez l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

L’exemple suivant montre la revendication retournée par l’API REST :

- Revendication **MembershipId** mappée au nom de la revendication **loyaltyNumber**.

Le profil technique retourne également des revendications, qui ne sont pas retournées par le fournisseur d’identité :

- Revendication **loyaltyNumberIsNew** qui a comme la valeur par défaut `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ServiceUrl | Oui | URL du point de terminaison de l’API REST. |
| AuthenticationType | Oui | Type de l’authentification effectuée par le fournisseur de revendications RESTful. Valeurs possibles : `None`, `Basic`, `Bearer` ou `ClientCertificate`. La valeur `None` indique que l’API REST n’est pas anonyme. La valeur `Basic` indique que l’API REST est sécurisée avec une authentification HTTP de base. Seuls des utilisateurs vérifiés, notamment Azure AD B2C, peuvent accéder à votre API. La valeur `ClientCertificate` (recommandée) indique que l’API REST restreint l’accès à l’aide d’une authentification de certificat client. Seuls des services disposant des certificats appropriés, par exemple Azure AD B2C, peuvent accéder à votre API. La valeur `Bearer` indique que l’API REST restreint l’accès à l’aide d’un jeton du porteur OAuth2 du client. |
| SendClaimsIn | Non | Spécifie la façon dont les revendications d’entrée sont envoyées au fournisseur de revendications RESTful. Valeurs possibles : `Body` (par défaut), `Form`, `Header`, ou `QueryString`. La valeur `Body` est la revendication d’entrée envoyée dans le corps de la demande au format JSON. Le valeur `Form` est la revendication d’entrée envoyée dans le corps de la demande, dans un format de valeurs de clé séparées par des perluètes (&). La valeur `Header` est la revendication d’entrée envoyée dans l’en-tête de la demande. La valeur `QueryString` est la revendication d’entrée envoyée dans la chaîne de requête de la demande. Les verbes HTTP appelés par chacune sont les suivants :<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Non | Spécifie le format des revendications de sortie. Valeurs possibles : `Body` (par défaut), `Form`, `Header`, ou `QueryString`. La valeur `Body` est la revendication de sortie envoyée dans le corps de la demande au format JSON. Le valeur `Form` est la revendication de sortie envoyée dans le corps de la demande, dans un format de valeurs de clé séparées par des perluètes (&). La valeur `Header` est la revendication de sortie envoyée dans l’en-tête de la demande. La valeur `QueryString` est la revendication de sortie envoyée dans la chaîne de requête de la demande. |
| ClaimUsedForRequestPayload| Non | Nom d’une revendication de chaîne qui contient la charge utile à envoyer à l’API REST. |
| DebugMode | Non | Exécute le profil technique en mode débogage. Valeurs possibles : `true` ou `false` (par défaut). En mode débogage, l’API REST peut retourner plus d’informations. Consultez la section [Retour de message d’erreur](#returning-error-message). |
| IncludeClaimResolvingInClaimsHandling  | Non | Pour les revendications d’entrée et de sortie, spécifie si la [résolution des revendications](claim-resolver-overview.md) est incluse dans le profil technique. Valeurs possibles : `true` ou `false` (par défaut). Si vous souhaitez utiliser un programme de résolution des revendications dans le profil technique, définissez cette valeur sur `true`. |
| ResolveJsonPathsInJsonTokens  | Non | Indique si le profil technique résout les chemins d’accès JSON. Valeurs possibles : `true` ou `false` (par défaut). Utilisez ces métadonnées pour lire des données issues d’un élément JSON imbriqué. Dans un élément [OutputClaim](technicalprofiles.md#outputclaims), définissez `PartnerClaimType` sur l’élément de chemin d’accès JSON que vous souhaitez générer. Par exemple : `firstName.localized` ou `data.0.to.0.email`.|

## <a name="cryptographic-keys"></a>Clés de chiffrement

Si le type d’authentification est défini sur `None`, l’élément **CryptographicKeys** n’est pas utilisé.

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Si le type d’authentification est défini sur `Basic`, l’élément **CryptographicKeys** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Oui | Nom d’utilisateur utilisé pour l’authentification. |
| BasicAuthenticationPassword | Oui | Mot de passe utilisé pour l’authentification. |

L’exemple suivant montre un profil technique avec une authentification de base :

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Si le type d’authentification est défini sur `ClientCertificate`, l’élément **CryptographicKeys** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ClientCertificate | Oui | Certificat X509 (jeu de clés RSA) à utiliser pour l’authentification. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Si le type d’authentification est défini sur `Bearer`, l’élément **CryptographicKeys** contient l’attribut suivant :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Non | Jeton du porteur OAuth 2.0. |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>Retour de message d’erreur

Il se peut que votre API REST doive retourner un message d’erreur tel que « Utilisateur introuvable dans le système CRM ». Lorsqu'une erreur se produit, l’API REST doit retourner un message d’erreur HTTP 409 (code d’état Conflit) avec les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| version | Oui | 1.0.0 |
| status | Oui | 409 |
| code | Non | Code d’erreur provenant du fournisseur de point de terminaison RESTful, affiché quand `DebugMode` est activé. |
| requestId | Non | Identificateur de demande provenant du fournisseur de point de terminaison RESTful, affiché quand `DebugMode` est activé. |
| userMessage | Oui | Message d’erreur affiché à l’utilisateur. |
| developerMessage | Non | Description détaillée du problème et de la manière de le corriger, affiché quand `DebugMode` est activé. |
| moreInfo | Non | URI pointant vers des informations supplémentaires, affiché quand `DebugMode` est activé. |

L’exemple suivant montre une API REST qui retourne un message d’erreur au format JSON :

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

L’exemple suivant montre une classe C# qui retourne un message d’erreur :

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour obtenir des exemples d’utilisation d’un profil technique RESTful :

- [Intégrer les échanges de revendications d’API REST dans votre parcours utilisateur Azure Active Directory B2C comme validation d’une entrée de l’utilisateur](rest-api-claims-exchange-dotnet.md)
- [Sécuriser vos services RESTful à l’aide d’une authentification HTTP de base](secure-rest-api-dotnet-basic-auth.md)
- [Sécuriser votre service RESTful à l’aide de certificats clients](secure-rest-api-dotnet-certificate-auth.md)
- [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme validation d’une entrée de l’utilisateur](custom-policy-rest-api-claims-validation.md)
