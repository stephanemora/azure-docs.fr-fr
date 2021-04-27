---
title: Profils techniques d’accès conditionnel dans les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Informations de référence sur les stratégies personnalisées pour les profils techniques d’accès conditionnel dans Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721074"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Définir un profil technique d’accès conditionnel dans une stratégie personnalisée Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L’accès conditionnel Azure Active Directory (Azure AD) est l’outil qu’Azure AD B2C utilise pour réunir des signaux, prendre des décisions et appliquer des stratégies d’organisation. Automatiser l’évaluation des risques avec des conditions de stratégie signifie que les connexions risquées sont immédiatement identifiées et corrigées ou bloquées.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

L’attribut **Name** de l’élément **Protocol** doit être défini sur `Proprietary`. L’attribut **handler** doit contenir le nom complet de l’assembly de gestionnaire de protocole utilisé par Azure AD B2C :

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

L’exemple suivant présente un profil technique d’accès conditionnel :

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Évaluation de l’accès conditionnel

À chaque connexion, Azure AD B2C évalue toutes les stratégies et vérifie que toutes les conditions requises sont remplies avant d’accorder l’accès à l’utilisateur. Le blocage de l’accès prévaut sur tous les autres paramètres de configuration. Le mode **Évaluation** du profil technique d’accès conditionnel évalue les signaux collectés par Azure AD B2C lors de la connexion avec un compte local. Le résultat du profil technique d’accès conditionnel est un ensemble de revendications qui résultent de l’évaluation de l’accès conditionnel. La stratégie Azure AD B2C utilise ces revendications dans une étape d’orchestration suivante pour prendre une mesure, telle que bloquer l’utilisateur ou exiger une authentification multifacteur. Les options suivantes peuvent être configurées pour ce mode.

### <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| OperationType | Oui | Doit être **Évaluation**.  |

### <a name="input-claims"></a>Revendications d’entrée

L’élément **InputClaims** contient une liste de revendications à envoyer à l’accès conditionnel. Vous pouvez également faire correspondre le nom de votre revendication au nom défini dans le profil technique d’accès conditionnel.

| ClaimReferenceId | Obligatoire | Type de données | Description |
| --------- | -------- | ----------- |----------- |
| UserId | Oui | string | Identificateur de l’utilisateur qui se connecte. |
| AuthenticationMethodsUsed | Oui |stringCollection | Liste des méthodes que l’utilisateur a utilisées pour se connecter. Valeurs possibles : `Password` et `OneTimePasscode`. |
| IsFederated | Oui |boolean | Indique si un utilisateur s’est connecté avec un compte fédéré. La valeur doit être `false`. |
| IsMfaRegistered | Oui |boolean | Indique si l’utilisateur a déjà inscrit un numéro de téléphone pour l’authentification multifacteur. |


L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** utilisés pour modifier les revendications d’entrée ou en générer de nouvelles avant de les envoyer au service d’accès conditionnel.

### <a name="output-claims"></a>Revendications de sortie

L’élément **OutputClaims** contient une liste de revendications générées par ConditionalAccessProtocolProvider. Vous pouvez également mapper le nom de votre revendication au nom défini ci-dessous.

| ClaimReferenceId | Obligatoire | Type de données | Description |
| --------- | -------- | ----------- |----------- |
| Défis | Oui |stringCollection | Liste d’actions pour corriger la menace identifiée. Valeurs possibles : `block` |
| MultiConditionalAccessStatus | Oui | stringCollection |  |

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

### <a name="example-evaluation"></a>Exemple : Évaluation

L’exemple suivant présente un profil technique d’accès conditionnel utilisé pour évaluer la menace de connexion.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Mise à jour

Le mode **Correction** du profil technique d’accès conditionnel informe Azure AD B2C que la menace de connexion identifiée a été corrigée. Les options suivantes peuvent être configurées pour le mode de correction.

### <a name="metadata"></a>Métadonnées

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| OperationType | Oui | Doit être **Correction**.  |

### <a name="input-claims"></a>Revendications d’entrée

L’élément **InputClaims** contient une liste de revendications à envoyer au service d’accès conditionnel. Vous pouvez également mapper le nom de votre revendication au nom défini dans le profil d’accès conditionnel.

| ClaimReferenceId | Obligatoire | Type de données | Description |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | Oui | stringCollection| Liste des défis relevés pour corriger la menace identifiée lorsque le retour du mode d’évaluation conteste la revendication.|


L’élément **InputClaimsTransformations** peut contenir une collection d’éléments **InputClaimsTransformation** utilisés pour modifier les revendications d’entrée ou en générer de nouvelles avant l’appel du service d’accès conditionnel.

### <a name="output-claims"></a>Revendications de sortie

Le fournisseur du protocole d’accès conditionnel ne retournant pas d’élément **OutputClaims**, il n’est pas nécessaire de spécifier des revendications de sortie. Vous pouvez toutefois inclure des revendications non retournées par le fournisseur du protocole d’accès conditionnel, à condition de définir l’attribut `DefaultValue`.

L’élément **OutputClaimsTransformations** peut contenir une collection d’éléments **OutputClaimsTransformation** qui sont utilisés pour modifier les revendications de sortie ou en générer de nouvelles.

### <a name="example-remediation"></a>Exemple : Correction

L’exemple suivant présente un profil technique d’accès conditionnel utilisé pour mitiger la menace de connexion.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Ajouter l’accès conditionnel à des flux d’utilisateurs dans Azure Active Directory B2C](conditional-access-user-flow.md).
