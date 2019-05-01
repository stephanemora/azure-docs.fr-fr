---
title: Exemples de transformations de revendications booléennes pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C | Microsoft Docs
description: Exemples de transformations de revendications booléennes pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 8afe530a2194fd8c2137f6d4aa7ac97fa8ed9a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695106"
---
# <a name="boolean-claims-transformations"></a>Transformations de revendications booléennes

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications booléennes du schéma Infrastructure d’expérience d’identité dans Azure Active Directory (Azure AD) B2C. Pour plus d’informations, consultez [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Effectue une opération And de deux inputClaims booléennes et définit outputClaim avec le résultat de l’opération.

| Item  | TransformationClaimType  | Type de données  | Notes |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | booléenne | Premier ClaimType à évaluer. |
| InputClaim | inputClaim2  | booléenne | Deuxième ClaimType à évaluer. |
|OutputClaim | outputClaim | booléenne | ClaimTypes qui seront produits après l’appel de cette transformation de revendication (true ou false). |

La transformation de revendication suivante montre comment exécuter une opération And sur deux ClaimTypes booléens : `isEmailNotExist`, et `isSocialAccount`. La revendication de sortie `presentEmailSelfAsserted` prend la valeur `true` si la valeur de ces deux revendications d’entrée est `true`. Dans une étape d’orchestration, vous pouvez utiliser une condition préalable pour prédéfinir une page autodéclarée, uniquement si un e-mail de compte social est vide.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim1** : true
    - **inputClaim2** : false
- Revendications de sortie :
    - **outputClaim** : false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Vérifie que les valeurs booléennes de deux revendications sont égales et lève une exception si elles ne le sont pas.

| Item | TransformationClaimType  | Type de données  | Notes |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | booléenne | ClaimType à évaluer. |
| InputParameter |valueToCompareTo | booléenne | Valeur à comparer (true ou false). |

La transformation de revendication **AssertBooleanClaimIsEqualToValue** est toujours exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) qui est appelé par un [profil technique autodéclaré](self-asserted-technical-profile.md). Les métadonnées de profil technique autodéclaré **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** contrôlent le message d’erreur présenté à l’utilisateur par le profil technique.

![Exécution d’AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

La transformation de revendication suivante montre comment vérifier la valeur d’un ClaimType booléen avec une valeur `true`. Si la valeur du ClaimType `accountEnabled` est false, un message d’erreur est levé.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Le profil technique de validation `login-NonInteractive` appelle la transformation de revendication `AssertAccountEnabledIsTrue`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Le profil technique autodéclaré appelle le profil technique de validation **login-NonInteractive**.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim** : false
    - **valueToCompareTo** : true
- Résultat : Erreur levée

## <a name="notclaims"></a>NotClaims

Effectue une opération Not de l’inputClaim booléen et définit outputClaim avec le résultat de l’opération.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | booléenne | Revendication à traiter. |
| OutputClaim | outputClaim | booléenne | ClaimTypes qui sont produits après l’appel de cette ClaimsTransformation (true ou false). |

Utilisez cette transformation de revendication pour effectuer une négation logique sur une revendication.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim** : false
- Revendications de sortie :
    - **outputClaim** : true

## <a name="orclaims"></a>OrClaims 

Calcule une opération Or de deux inputClaims booléennes et définit outputClaim avec le résultat de l’opération.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | booléenne | Premier ClaimType à évaluer. |
| InputClaim | inputClaim2 | booléenne | Deuxième ClaimType à évaluer. |
| OutputClaim | outputClaim | booléenne | ClaimTypes qui seront produits après l’appel de cette ClaimsTransformation (true ou false). |

La transformation de revendication suivante montre comment effectuer une opération `Or` sur deux ClaimTypes booléens. Dans l’étape d’orchestration, vous pouvez utiliser une condition préalable pour prédéfinir une page autodéclarée, si la valeur de l’une des revendications est `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim1** : true
    - **inputClaim2** : false
- Revendications de sortie :
    - **outputClaim** : true

