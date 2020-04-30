---
title: Définir un profil technique pour un émetteur SAML dans une stratégie personnalisée
titleSuffix: Azure AD B2C
description: Définissez un profil technique pour un émetteur de jeton SAML (Security Assertion Markup Language) dans une stratégie personnalisée d’Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f7beccde92030d1e01633f4e4044849d7e91d05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82229950"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique pour un émetteur de jeton SAML dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) émet plusieurs types de jetons de sécurité lors du traitement de chaque flux d’authentification. Un profil technique pour un émetteur de jeton SAML émet un jeton SAML qui est retourné à l’application par partie de confiance (le fournisseur d’identité). Ce profil technique est généralement la dernière étape d’orchestration dans le parcours utilisateur.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `None`. Définissez l’élément **OutputTokenFormat** sur `SAML2`.

L’exemple suivant montre un profil technique pour `Saml2AssertionIssuer` :

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Revendications d’entrée, de sortie et de conservation

Les éléments **InputClaims**, **OutputClaims** et **PersistClaims** sont vides ou absents. Les éléments **InutputClaimsTransformations** et **OutputClaimsTransformations** sont également absents.

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| IssuerUri | Non | Nom de l’émetteur qui apparaît dans la réponse SAML. La valeur doit être identique au nom configuré dans l’application par partie de confiance. |

## <a name="cryptographic-keys"></a>Clés de chiffrement

L’élément CryptographicKeys contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| MetadataSigning | Oui | Certificat X509 (jeu de clés RSA) à utiliser pour signer les métadonnées SAML. Azure AD B2C utilise cette clé pour signer les métadonnées. |
| SamlMessageSigning| Oui| Précisez le certificat X509 (jeu de clés RSA) à utiliser pour signer les messages SAML. Azure AD B2C utilise cette clé pour signer la réponse `<samlp:Response>` envoyée à la partie de confiance.|

## <a name="session-management"></a>Gestion des sessions

Pour configurer les sessions SAML Azure AD B2C entre une application par partie de confiance et l’attribut de l’élément `UseTechnicalProfileForSessionManagement`, référencez une session SSO [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants pour obtenir des exemples d’utilisation d’un profil technique d’émetteur SAML :

- [Inscrire une application SAML dans Azure AD B2C](connect-with-saml-service-providers.md)












