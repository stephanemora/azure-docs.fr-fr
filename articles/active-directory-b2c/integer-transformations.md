---
title: Exemples de transformations de revendications d’entier pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C | Microsoft Docs
description: Exemples de transformations de revendications d’entier pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d82f7fecfb35c63d586993fed73a83209782a890
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064216"
---
# <a name="integer-claims-transformations"></a>Transformations de revendications d’entier

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications d’entier du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

Convertit un type de données long en type de données string.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | ClaimType à convertir en une chaîne. |
| OutputClaim | outputClaim | string | ClaimType généré après l’appel de cette ClaimsTransformation. |

Dans cet exemple, la revendication `numericUserId` avec un type de valeur long est convertie en revendication `UserId` avec un type de valeur chaîne.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
    - **inputClaim** : 12334 (long)
- Revendications de sortie :
    - **outputClaim** : "12334" (chaîne)

