---
title: Exemples de transformation de revendications d’entier pour les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Exemples de transformations de revendications d’entier pour le schéma IEF (Identity Experience Framework) d’Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: dc637e6369a1dbaaa9a25ad5d7b91b7b5d95ffd6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "131036392"
---
# <a name="integer-claims-transformations"></a>Transformations de revendications d’entier

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications d’entier du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="adjustnumber"></a>AdjustNumber

Augmente ou diminue une revendication numérique et renvoie une nouvelle revendication.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | Type de revendication, qui contient le nombre à augmenter ou à diminuer. Si la valeur de la réclamation `inputClaim` est Null, la valeur par défaut 0 est utilisée. |
| InputParameter | Opérateur | string | Valeurs possibles : `INCREMENT` (par défaut) ou `DECREMENT`.|
| OutputClaim | outputClaim | int | Type de revendication généré une fois que cette transformation de revendications a été appelée. |

Utilisez cette transformation de revendication pour augmenter ou diminuer une valeur de revendication numérique. 

```xml
<ClaimsTransformation Id="UpdateSteps" TransformationMethod="AdjustNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="steps" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="INCREMENT" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="steps" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Exemple 1

- Revendications d’entrée :
    - **inputClaim** : 1
- Paramètres d’entrée :
    - **Opérateur** : INCREMENT
- Revendications de sortie :
    - **outputClaim** : 2

### <a name="example-2"></a>Exemple 2

- Revendications d’entrée :
    - **inputClaim** : NULL
- Paramètres d’entrée :
    - **Opérateur** : INCREMENT
- Revendications de sortie :
    - **outputClaim** : 1


## <a name="assertnumber"></a>AssertNumber

Détermine si une revendication numérique est supérieure, inférieure, égale ou différente d’un nombre. 

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | int | Première revendication numérique comparant si elle est supérieure, inférieure, égale ou différente du deuxième nombre. Une valeur null lève une exception. |
| InputParameter | CompareToValue | int | Deuxième nombre comparant s’il est supérieur, inférieur, égal ou différent du premier nombre. |
| InputParameter | Opérateur | string | Valeurs possibles : `LESSTHAN`, `GREATERTHAN`, `GREATERTHANOREQUAL`, `LESSTHANOREQUAL`, `EQUAL`, `NOTEQUAL`. |
| InputParameter | throwError | boolean | Spécifie si cette assertion doit lever une erreur si le résultat de la comparaison est `true`. Valeurs possibles : `true` (par défaut) ou `false`. <br />&nbsp;<br />Si la valeur est `true` (mode d’assertion) et que le résultat de la comparaison est `true`, une exception est levée. Lorsque la valeur est `false` (mode d’évaluation), le résultat est un nouveau type de revendication booléenne avec une valeur `true` ou `false`.| 
| OutputClaim | outputClaim | boolean | Si `ThrowError` a la valeur `false`, cette revendication de sortie contient la valeur `true`, ou `false` selon le résultat de la comparaison. |

### <a name="assertion-mode"></a>Mode d’assertion

Lorsque le paramètre d’entrée `throwError` est défini sur `true` (par défaut), la transformation de revendications **AssertNumber** est toujours exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) appelé par un [profil technique autodéclaré](self-asserted-technical-profile.md). 

Les métadonnées du profil technique autodéclaré **AssertNumberError** contrôlent le message d’erreur présenté à l’utilisateur par le profil technique. Les messages d’erreur peuvent être [localisés](localization-string-ids.md#claims-transformations-error-messages).

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="AssertNumberError">You've reached the maximum logon attempts</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

Pour plus d’informations sur la façon d’appeler la transformation de revendications dans un mode d’assertion, consultez les transformations de revendications [AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal), [AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) et [AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan).

### <a name="assertion-mode-example"></a>Exemple de mode d’assertion

L’exemple suivant affirme que le nombre de tentatives est supérieur à cinq. La transformation de revendications lève une erreur en fonction du résultat de la comparaison. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```

- Revendications d’entrée :
    - **inputClaim** : 10
- Paramètres d’entrée :
    - **Opérateur** : GREATERTHAN
    - **CompareToValue** : 5
    - **throwError** : true
- Résultat : Erreur levée

### <a name="evaluation-mode-example"></a>Exemple de mode d’évaluation

L’exemple suivant évalue si le nombre de tentatives est supérieur à cinq. La revendication de sortie contient une valeur booléenne selon le résultat de la comparaison. La transformation de revendications ne lève pas d’erreur. 

```xml
<ClaimsTransformation Id="isOverLimit" TransformationMethod="AssertNumber">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="attempts" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="Operator" DataType="string" Value="GREATERTHAN" />
    <InputParameter Id="CompareToValue" DataType="int" Value="5" />
    <InputParameter Id="throwError" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="attemptsCountExceeded" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Revendications d’entrée :
    - **inputClaim** : 10
- Paramètres d’entrée :
    - **Opérateur** : GREATERTHAN
    - **CompareToValue** : 5
    - **throwError** : false
- Revendications de sortie :
    - **outputClaim** : true


## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Convertit un type de données long en type de données string.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | Type de revendication à convertir en chaîne. |
| OutputClaim | outputClaim | string | Type de revendication généré une fois que cette transformation de revendications a été appelée. |

Dans cet exemple, la revendication `numericUserId` avec un type de valeur long est convertie en revendication `UserId` avec un type de valeur chaîne.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
    - **inputClaim**: 12334 (long)
- Revendications de sortie :
    - **outputClaim**: "12334" (string)

