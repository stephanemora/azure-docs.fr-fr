---
title: Profils techniques Azure MFA dans les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Informations de référence sur les stratégies personnalisées pour les profils techniques Azure Multi-Factor Authentication (MFA) dans Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05851dba9de06b5dfba2da4f455fbaf5e9376d08
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184279"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Définir un profil technique Azure MFA dans une stratégie personnalisée Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) gère la vérification d’un numéro de téléphone à l’aide d’Azure Multi-Factor Authentication (MFA). Utilisez ce profil technique pour générer et envoyer un code à un numéro de téléphone, puis vérifier le code.

Le profil technique Azure MFA peut également retourner un message d’erreur. Vous pouvez concevoir l’intégration avec Azure MFA à l’aide d’un **profil technique de validation**. Un profil technique de validation appelle le service Azure MFA. Le profil technique de validation valide les données fournies par l’utilisateur avant la poursuite du parcours de celui-ci. Avec le profil technique de validation, un message d’erreur apparaît sur une page déclarée automatiquement.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom complet de l’assembly de gestionnaire de protocole utilisé par Azure AD B2C :

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

L’exemple suivant montre un profil technique Azure MFA :

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Envoyer un SMS

Le premier mode de ce profil technique consiste à générer un code et à l’envoyer. Les options suivantes peuvent être configurées pour ce mode.

### <a name="input-claims"></a>Revendications d’entrée

L’élément **InputClaims** contient la liste des revendications à envoyer à Azure MFA. Vous pouvez également faire correspondre le nom de votre revendication au nom défini dans le profil technique MFA.

| ClaimReferenceId | Obligatoire | Description |
| --------- | -------- | ----------- |
| userPrincipalName | Oui | Identificateur de l’utilisateur propriétaire du numéro de téléphone. |
| phoneNumber | Oui | Numéro de téléphone auquel le code SMS sera envoyé. |
| companyName | Non |Nom de la société dans le SMS. S’il n’est pas indiqué, c’est le nom de votre application qui est utilisé. |
| locale | Non | Paramètres régionaux du SMS. S’ils ne sont pas indiqués, ce sont les paramètres régionaux du navigateur de l’utilisateur qui sont utilisés. |

L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** utilisés pour modifier les revendications d’entrée ou en générer de nouvelles avant l’envoi au service Azure MFA.

### <a name="output-claims"></a>Revendications de sortie

Comme le fournisseur de protocole Azure MFA ne retourne pas de **OutputClaims**, il n’est pas nécessaire de spécifier des revendications de sortie. Vous pouvez toutefois inclure des revendications non retournées par le fournisseur d’identité Azure MFA, tant que vous définissez l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

### <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Opération | Oui | Doit être **OneWaySMS**.  |
| UserMessageIfInvalidFormat | Non | Message d’erreur personnalisé envoyé si le numéro de téléphone fourni n’est pas un numéro de téléphone valide. |
| UserMessageIfCouldntSendSms | Non | Message d’erreur personnalisé envoyé si le numéro de téléphone fourni n’accepte pas les SMS. |
| UserMessageIfServerError | Non | Message d’erreur personnalisé envoyé si le serveur a rencontré une erreur interne. |

### <a name="return-an-error-message"></a>Retourner un message d’erreur

Comme nous l’avons vu dans [Métadonnées](#metadata), vous pouvez personnaliser le message d’erreur présenté à l’utilisateur pour différents cas d’erreur. Vous pouvez également localiser ces messages en préfixant les paramètres régionaux. Par exemple :

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Exemple : envoyer un SMS

L’exemple suivant montre l’utilisation d’un profil technique Azure MFA pour envoyer un code par SMS.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">OneWaySMS</Item>
    </Metadata>
    <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
        <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
    </InputClaimsTransformations>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Vérifier le code

Le deuxième mode de ce profil technique consiste à vérifier un code. Les options suivantes peuvent être configurées pour ce mode.

### <a name="input-claims"></a>Revendications d’entrée

L’élément **InputClaims** contient la liste des revendications à envoyer à Azure MFA. Vous pouvez également faire correspondre le nom de votre revendication au nom défini dans le profil technique MFA.

| ClaimReferenceId | Obligatoire | Description |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Oui | Même numéro de téléphone qui a servi à envoyer un code. Il est également utilisé pour rechercher une session de vérification par téléphone. |
| verificationCode  | Oui | Code de vérification fourni par l’utilisateur à vérifier. |

L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** utilisés pour modifier les revendications d’entrée ou en générer de nouvelles avant l’appel du service Azure MFA.

### <a name="output-claims"></a>Revendications de sortie

Comme le fournisseur de protocole Azure MFA ne retourne pas de **OutputClaims**, il n’est pas nécessaire de spécifier des revendications de sortie. Vous pouvez toutefois inclure des revendications non retournées par le fournisseur d’identité Azure MFA, tant que vous définissez l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

## <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Opération | Oui | Doit être **Verify**. |
| UserMessageIfInvalidFormat | Non | Message d’erreur personnalisé envoyé si le numéro de téléphone fourni n’est pas un numéro de téléphone valide. |
| UserMessageIfWrongCodeEntered | Non | Message d’erreur personnalisé envoyé si le code entré pour la vérification est incorrect. |
| UserMessageIfMaxAllowedCodeRetryReached | Non | Message d’erreur personnalisé envoyé si l’utilisateur a effectué trop de tentatives de code de vérification. |
| UserMessageIfThrottled | Non | Message d’erreur personnalisé envoyé si l’utilisateur est limité. |
| UserMessageIfServerError | Non | Message d’erreur personnalisé envoyé si le serveur a rencontré une erreur interne. |

### <a name="return-an-error-message"></a>Retourner un message d’erreur

Comme nous l’avons vu dans [Métadonnées](#metadata), vous pouvez personnaliser le message d’erreur présenté à l’utilisateur pour différents cas d’erreur. Vous pouvez également localiser ces messages en préfixant les paramètres régionaux. Par exemple :

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Exemple : vérifier un code

L’exemple suivant montre l’utilisation d’un profil technique Azure MFA pour vérifier le code.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
