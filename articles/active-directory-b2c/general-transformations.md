---
title: Exemples de transformations de revendications générales pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C | Microsoft Docs
description: Exemples de transformations de revendications générales pour le schéma Infrastructure d’expérience d’identité d’Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4e28dff6235e869c9275a8b0ba8d80252a9ea792
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167374"
---
# <a name="general-claims-transformations"></a>Transformations de revendications générales

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications générales du schéma Infrastructure d’expérience d’identité dans Azure Active Directory (Azure AD) B2C. Pour plus d’informations, consultez [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Vérifie si l’**inputClaim** existe, et définit **outputClaim** sur true ou false en conséquence.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Quelconque | Revendication d’entrée dont l’existence doit être vérifiée. |
| OutputClaim | outputClaim | booléenne | ClaimType généré après l’appel de cette ClaimsTransformation. |

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

Hache le texte brut fourni à l’aide de la valeur salt et d’un secret.

| Item | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | chaîne | Revendication d’entrée à chiffrer. |
| InputClaim | salt | chaîne | Paramètre salt. Vous pouvez créer une valeur aléatoire à l’aide de la transformation des revendication `CreateRandomString`. |
| InputParameter | randomizerSecret | chaîne | Pointe vers une **clé de stratégie** Azure AD B2C existante. Pour en créer une nouvelle : Dans votre locataire Azure AD B2C, sélectionnez **Paramètres B2C > Identity Experience Framework**. Sélectionnez **Clés de stratégie** pour afficher les clés qui sont disponibles dans votre locataire. Sélectionnez **Ajouter**. Pour **Options**, sélectionnez **Manuel**. Fournissez un nom (il est possible que le préfixe B2C_1A_ soit ajouté automatiquement). Dans la zone Secret, entrez un secret à utiliser, tel que 1234567890. Pour Utilisation de la clé, sélectionnez **Secret**. Sélectionnez **Créer**. |
| OutputClaim | Hachage | chaîne | ClaimType généré après que cette transformation de revendication a été appelée. Revendication configurée dans l’inputClaim `plaintext`. |

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



