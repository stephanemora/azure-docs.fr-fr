---
title: Activer la vérification du mot de passe à usage unique (OTP)
titleSuffix: Azure AD B2C
description: Découvrez comment configurer un scénario de mot de passe à usage unique (OTP) en utilisant des stratégies personnalisées Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9becb91cfffd4553b2b8aa1a2d616963eae92ab0
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114056"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Définir un profil technique de mot de passe à usage unique dans une stratégie personnalisée Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) gère la génération et la vérification d'un mot de passe à usage unique. Utilisez un profil technique pour générer un code, puis vérifiez ce code plus tard.

Le profil technique de mot de passe à usage unique peut également renvoyer un message d'erreur pendant la vérification du code. Concevez l’intégration avec le mot de passe à usage unique en utilisant un **profil technique de validation**. Un profil technique de validation appelle le profil technique de mot de passe à usage unique pour vérifier le code. Le profil technique de validation valide les données fournies par l’utilisateur avant la poursuite du parcours de celui-ci. Avec le profil technique de validation, un message d’erreur apparaît sur une page déclarée automatiquement.

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom qualifié complet d’Assembly de gestionnaire de protocole utilisé par Azure AD B2C :

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

L’exemple suivant montre un profil technique de mot de passe à usage unique :

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Générer le code

Le premier mode de ce profil technique consiste à générer un code. Vous trouverez ci-dessous les options qui peuvent être configurées pour ce mode.

### <a name="input-claims"></a>Revendications d’entrée

L'élément **InputClaims** contient une liste de revendications à envoyer au fournisseur du protocole de mot de passe à usage unique. Vous pouvez également mapper le nom de votre revendication au nom défini ci-dessous.

| ClaimReferenceId | Obligatoire | Description |
| --------- | -------- | ----------- |
| identificateur | Oui | Identificateur permettant d’identifier l'utilisateur qui doit vérifier le code ultérieurement. Il est généralement utilisé comme l'identificateur de la destination où le code est livré, par exemple l'adresse e-mail ou le numéro de téléphone. |

L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles avant l’envoi au fournisseur du protocole de mot de passe à usage unique.

### <a name="output-claims"></a>Revendications de sortie

L'élément **OutputClaims** contient une liste de revendications générées par le fournisseur du protocole de mot de passe à usage unique. Vous pouvez également mapper le nom de votre revendication au nom défini ci-dessous.

| ClaimReferenceId | Obligatoire | Description |
| --------- | -------- | ----------- |
| otpGenerated | Oui | Code généré dont la session est gérée par Azure AD B2C. |

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

### <a name="metadata"></a>Métadonnées

Les paramètres suivants peuvent être utilisés pour configurer la génération et la maintenance du code :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Non | Délai d’expiration du code, en secondes. Minimum : `60` ; maximum : `1200` ; par défaut : `600`. |
| CodeLength | Non | Longueur du code. La valeur par défaut est `6`. |
| CharacterSet | Non | Jeu de caractères pour le code, formaté pour être utilisé dans une expression régulière. Par exemple : `a-z0-9A-Z`. La valeur par défaut est `0-9`. Le jeu de caractères doit comprendre un minimum de 10 caractères différents dans le jeu spécifié. |
| NumRetryAttempts | Non | Nombre de tentatives de vérification avant que le code soit considéré comme non valide. La valeur par défaut est `5`. |
| Opération | Oui | Opération à effectuer. Valeurs possibles : `GenerateCode` ou `VerifyCode`. |
| ReuseSameCode | Non | Spécifie si un code en double doit être fourni au lieu de générer un nouveau code lorsque le code donné n'a pas expiré et reste valide. La valeur par défaut est `false`. |

### <a name="returning-error-message"></a>Retour de message d’erreur

Aucun message d'erreur n’est renvoyé pour le mode de génération de code.

### <a name="example"></a>Exemple

L'exemple suivant `TechnicalProfile` est utilisé pour générer un code :

```XML
<TechnicalProfile Id="GenerateCode">
    <DisplayName>Generate Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">600</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="NumRetryAttempts">5</Item>
        <Item Key="ReuseSameCode">false</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
    </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Vérifier le code

Le deuxième mode de ce profil technique consiste à vérifier un code. Vous trouverez ci-dessous les options qui peuvent être configurées pour ce mode.

### <a name="input-claims"></a>Revendications d’entrée

L'élément **InputClaims** contient une liste de revendications à envoyer au fournisseur du protocole de mot de passe à usage unique. Vous pouvez également mapper le nom de votre revendication au nom défini ci-dessous.

| ClaimReferenceId | Obligatoire | Description |
| --------- | -------- | ----------- |
| identificateur | Oui | Identificateur permettant d’identifier l'utilisateur qui a précédemment généré un code. Il est généralement utilisé comme l'identificateur de la destination où le code est livré, par exemple l'adresse e-mail ou le numéro de téléphone. |
| otpToVerify | Oui | Code de vérification fourni par l’utilisateur. |

L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles avant l’envoi au fournisseur du protocole de mot de passe à usage unique.

### <a name="output-claims"></a>Revendications de sortie

Aucune revendication de sortie n’est fournie pendant la vérification du code de ce fournisseur de protocole.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

### <a name="metadata"></a>Métadonnées

Les paramètres suivants peuvent être utilisés pour configurer le message d'erreur affiché en cas d'échec de la vérification du code :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Non | Message à afficher à l'utilisateur si la session de vérification du code a expiré. Indique soit que le code a expiré, soit que le code n'a jamais été généré pour un identificateur donné. |
| UserMessageIfMaxRetryAttempted | Non | Message à afficher à l'utilisateur s'il a dépassé les tentatives de vérification maximales autorisées. |
| UserMessageIfInvalidCode | Non | Message à afficher à l'utilisateur s'il a fourni un code non valide. |

### <a name="returning-error-message"></a>Retour de message d’erreur

Comme nous l’avons vu dans [Métadonnées](#metadata), vous pouvez personnaliser le message d’erreur présenté à l’utilisateur pour différents cas d’erreur. Vous pouvez également localiser ces messages en préfixant les paramètres régionaux, par exemple :

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>Exemple

L'exemple suivant `TechnicalProfile` est utilisé pour la vérification d'un code :

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Étapes suivantes

Consultez l'article suivant pour accéder à un exemple d'utilisation de profil technique avec mot de passe à usage unique et vérification par e-mail personnalisée :

- [Vérification par e-mail personnalisée dans Azure Active Directory B2C](custom-email.md)

