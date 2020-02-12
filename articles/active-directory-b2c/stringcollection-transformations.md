---
title: Exemples de transformation de revendications StringCollection pour les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Exemples de transformations de revendications StringCollection pour le schéma Identity Experience Framework (IEF) d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8f91db91eff3320691a5979d9453bf515ccd59a2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982294"
---
# <a name="stringcollection-claims-transformations"></a>Transformations de revendications StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation des transformations de revendications de collections de chaînes du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Ajoute une revendication de chaîne à une nouvelle revendication stringCollection.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | ClaimType à ajouter à la revendication de sortie. |
| InputClaim | collection | stringCollection | [Facultatif] Si spécifié, la transformation de revendication copie les éléments de cette collection et ajoute l’élément à la fin de la revendication de collection de sortie. |
| OutputClaim | collection | stringCollection | ClaimTypes générés après l’appel de cette ClaimsTransformation. |

Utilisez cette transformation de revendication pour ajouter une chaîne à un objet stringCollection nouveau ou existant. Elle est couramment utilisée dans un profil technique **AAD-UserWriteUsingAlternativeSecurityId**. Avant la création d’un compte social, la transformation de revendication **CreateOtherMailsFromEmail** lit le ClaimType et ajoute la valeur au ClaimType **otherMails**.

La transformation de revendication suivante ajoute le ClaimType **e-mail** au ClaimType **otherMails**.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **collection** : ["someone@outlook.com"]
  - **item** : "admin@contoso.com"
- Revendications de sortie :
  - **collection** : ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Ajoute un paramètre de chaîne à une nouvelle revendication stringCollection.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [Facultatif] Si spécifié, la transformation de revendication copie les éléments de cette collection et ajoute l’élément à la fin de la revendication de collection de sortie. |
| InputParameter | item | string | Valeur à ajouter à la revendication de sortie. |
| OutputClaim | collection | stringCollection | ClaimTypes qui sont générés après l’appel de cette ClaimsTransformation. |

Utilisez cette transformation de revendication pour ajouter une valeur de chaîne à un objet stringCollection nouveau ou existant. L’exemple suivant ajoute une adresse e-mail constante (admin@contoso.com) à la revendication **otherMails**.

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **collection** : ["someone@outlook.com"]
- Paramètres d'entrée
  - **item** : "admin@contoso.com"
- Revendications de sortie :
  - **collection** : ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtient le premier élément de la collection de chaînes fournie.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | ClaimTypes qui sont utilisés par la transformation de revendication pour obtenir l’élément. |
| OutputClaim | extractedItem | string | ClaimTypes générés après l’appel de cette ClaimsTransformation. Premier élément de la collection. |

L’exemple suivant lit la revendication **otherMails** et retourne le premier élément dans la revendication **e-mail**.

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **collection** : ["someone@outlook.com", "someone@contoso.com"]
- Revendications de sortie :
  - **extractedItem** : "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

Vérifie si un type de revendication StringCollection contient un élément

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Type de revendication dans lequel effectuer la recherche. |
|InputParameter|item|string|Valeur à rechercher.|
|InputParameter|ignoreCase|string|Spécifie si cette comparaison doit ignorer la casse des chaînes comparées.|
| OutputClaim | outputClaim | boolean | ClaimType généré après l’appel de cette ClaimsTransformation. Indicateur booléen si la collection contient une telle chaîne |

L’exemple suivant vérifie si le type de revendication stringCollection `roles` contient la valeur **admin**.

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

- Revendications d’entrée :
    - **inputClaim** : ["reader", "author", "admin"]
- Paramètres d’entrée :
    - **item** : "Admin"
    - **ignoreCase** : "true"
- Revendications de sortie :
    - **outputClaim** : "true"


