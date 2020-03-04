---
title: Exemples de transformation de revendications booléennes pour les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Exemples de transformations de revendications booléennes pour le schéma Identity Experience Framework (IEF) d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 130fca4d5894316e7684270ff9d6361e9d9f9dd3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620841"
---
# <a name="boolean-claims-transformations"></a>Transformations de revendications booléennes

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications booléennes du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Effectue une opération And de deux inputClaims booléennes et définit outputClaim avec le résultat de l’opération.

| Élément  | TransformationClaimType  | Type de données  | Notes |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | Premier ClaimType à évaluer. |
| InputClaim | inputClaim2  | boolean | Deuxième ClaimType à évaluer. |
|OutputClaim | outputClaim | boolean | ClaimTypes qui seront produits après l’appel de cette transformation de revendication (true ou false). |

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

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputClaim1** : true
    - **inputClaim2** : false
- Revendications de sortie :
    - **outputClaim** : false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Vérifie que les valeurs booléennes de deux revendications sont égales et lève une exception si elles ne le sont pas.

| Élément | TransformationClaimType  | Type de données  | Notes |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | ClaimType à évaluer. |
| InputParameter |valueToCompareTo | boolean | Valeur à comparer (true ou false). |

La transformation de revendication **AssertBooleanClaimIsEqualToValue** est toujours exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) qui est appelé par un [profil technique autodéclaré](self-asserted-technical-profile.md). Les métadonnées de profil technique autodéclaré **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** contrôlent le message d’erreur présenté à l’utilisateur par le profil technique.

![Exécution de AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

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

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputClaim** : false
    - **valueToCompareTo** : true
- Résultat : Erreur levée

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Vérifie que la valeur booléenne d’une revendication est égale à `true` ou `false`, et retourne le résultat de la compression. 

| Élément | TransformationClaimType  | Type de données  | Notes |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | ClaimType à évaluer. |
| InputParameter |valueToCompareTo | boolean | Valeur à comparer (true ou false). |
| OutputClaim | compareResult | boolean | ClaimType généré après l’appel de cette ClaimsTransformation. |


La transformation de revendication suivante montre comment vérifier la valeur d’un ClaimType booléen avec une valeur `true`. Si la valeur du ClaimType `IsAgeOver21Years` est égale à `true`, la transformation de revendication retourne la valeur `true`, sinon `false`.

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputClaim** : false
- Paramètres d’entrée :
    - **valueToCompareTo** : true
- Revendications de sortie :
    - **compareResult** : false 



## <a name="notclaims"></a>NotClaims

Effectue une opération Not de l’inputClaim booléen et définit outputClaim avec le résultat de l’opération.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | Revendication à traiter. |
| OutputClaim | outputClaim | boolean | ClaimTypes qui sont produits après l’appel de cette ClaimsTransformation (true ou false). |

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

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputClaim** : false
- Revendications de sortie :
    - **outputClaim** : true

## <a name="orclaims"></a>OrClaims

Calcule une opération Or de deux inputClaims booléennes et définit outputClaim avec le résultat de l’opération.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | Premier ClaimType à évaluer. |
| InputClaim | inputClaim2 | boolean | Deuxième ClaimType à évaluer. |
| OutputClaim | outputClaim | boolean | ClaimTypes qui seront produits après l’appel de cette ClaimsTransformation (true ou false). |

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

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputClaim1** : true
    - **inputClaim2** : false
- Revendications de sortie :
    - **outputClaim** : true
