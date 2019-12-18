---
title: Exemples de transformation de revendications générales pour les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Exemples de transformations de revendications générales pour le schéma Identity Experience Framework (IEF) d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 639277177bf63e659e5b0ea804eca5e20f956831
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948837"
---
# <a name="general-claims-transformations"></a>Transformations de revendications générales

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications générales du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Vérifie si l’**inputClaim** existe, et définit **outputClaim** sur true ou false en conséquence.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Quelconque | Revendication d’entrée dont l’existence doit être vérifiée. |
| OutputClaim | outputClaim | boolean | ClaimType généré après l’appel de cette ClaimsTransformation. |

Utilisez cette transformation de revendication pour vérifier si une revendication existe ou si elle contient une valeur quelconque. La valeur de retour est une valeur booléenne qui indique si la revendication existe. L’exemple suivant vérifie si l’adresse e-mail existe.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
  - **inputClaim** : someone@contoso.com
- Revendications de sortie :
  - **outputClaim** : true

## <a name="hash"></a>Hachage

Hache le texte brut fourni à l’aide de la valeur salt et d’un secret. L’algorithme de hachage utilisé est SHA-256.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | Revendication d’entrée à chiffrer. |
| InputClaim | salt | string | Paramètre salt. Vous pouvez créer une valeur aléatoire à l’aide de la transformation des revendication `CreateRandomString`. |
| InputParameter | randomizerSecret | string | Pointe vers une **clé de stratégie** Azure AD B2C existante. Pour créer une clé de stratégie : Dans votre locataire Azure AD B2C, sous **Gérer**, sélectionnez **Identity Experience Framework**. Sélectionnez **Clés de stratégie** pour afficher les clés qui sont disponibles dans votre locataire. Sélectionnez **Ajouter**. Pour **Options**, sélectionnez **Manuel**. Fournissez un nom (il est possible que le préfixe *B2C_1A_* soit ajouté automatiquement). Dans la zone de texte **Secret**, entrez un secret à utiliser, tel que 1234567890. Pour **Utilisation de la clé**, sélectionnez **Signature**. Sélectionnez **Create** (Créer). |
| OutputClaim | Hachage | string | ClaimType généré après que cette transformation de revendication a été appelée. Revendication configurée dans l’inputClaim `plaintext`. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemples

- Revendications d’entrée :
  - **plaintext** : MyPass@word1
  - **salt** : 487624568
  - **randomizerSecret** : B2C_1A_AccountTransformSecret
- Revendications de sortie :
  - **outputClaim** : CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
