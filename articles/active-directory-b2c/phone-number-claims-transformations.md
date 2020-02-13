---
title: Transformations de revendications de numéro de téléphone dans des stratégies personnalisées
titleSuffix: Azure AD B2C
description: Référence de stratégie personnalisée pour les transformations de revendications de numéro de téléphone dans Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 38763f414b1e5373af79d2501850a44e8e813451
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185470"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Définir des transformations de revendications de numéro de téléphone dans Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article contient des informations et des exemples pour l’utilisation de transformations de revendications de numéro de téléphone du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations sur les transformations de revendications en général, voir [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Cette revendication valide le format du numéro de téléphone. Si son format est valide, remplacez-le par un format standard utilisé par Azure AD B2C. Si le format n’est pas valide, un message d’erreur est renvoyé.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string | Revendication du type de chaîne à partir duquel la conversion est effectuée. |
| OutputClaim | outputClaim | phoneNumber | Résultat de cette transformation de revendications. |

La transformation de revendications **ConvertStringToPhoneNumberClaim** est toujours exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) appelé par un [profil technique autodéclaré](self-asserted-technical-profile.md) ou un [contrôle d’affichage](display-controls.md). Les métadonnées du profil technique autodéclaré **UserMessageIfClaimsTransformationInvalidPhoneNumber** contrôlent le message d’erreur présenté à l’utilisateur.

![Diagramme du chemin d’exécution du message d’erreur](./media/phone-authentication/assert-execution.png)

Vous pouvez utiliser cette transformation de revendications pour vous assurer que la revendication de chaîne fournie est un numéro de téléphone valide. Si ce n’est pas le cas, un message d’erreur est levé. L’exemple suivant vérifie que le type de revendication **phoneString** est effectivement un numéro de téléphone valide, puis renvoie le numéro de téléphone au format Azure AD B2C standard. Si ce n’est pas le cas, un message d’erreur est levé.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Le profil technique auto-déclaré qui appelle le profil technique de validation contenant cette transformation de revendications peut définir le message d’erreur.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **inputClaim** : +1 (123) 456-7890
- Revendications de sortie :
  - **outputClaim** : +11234567890

## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Cette chaîne extrait l’indicatif du pays et le numéro national de la revendication d’entrée, et lève éventuellement une exception si le numéro de téléphone fourni n’est pas valide.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | string | Revendication de chaîne du numéro de téléphone. Le numéro de téléphone doit être au format international, précédé du signe « + » et d’un indicatif de pays. |
| InputParameter | throwExceptionOnFailure | boolean | [Facultatif] Paramètre indiquant si une exception est levée lorsque le numéro de téléphone n’est pas valide. La valeur par défaut est false. |
| InputParameter | countryCodeType | string | [Facultatif] Paramètre indiquant le type d’indicatif de pays dans la revendication de sortie. Les valeurs disponibles sont **CallingCode** (le code d’appel international d’un pays, par exemple + 1) ou **ISO3166** (code ISO-3166 de deux lettres du pays). |
| OutputClaim | nationalNumber | string | Revendication de chaîne pour le numéro national du numéro de téléphone. |
| OutputClaim | countryCode | string | Revendication de chaîne pour l’indicatif de pays du numéro de téléphone. |


Si la transformation de revendications **GetNationalNumberAndCountryCodeFromPhoneNumberString** est exécutée à partir d’un [profil technique de validation](validation-technical-profile.md) appelé par un [profil technique auto-déclaré](self-asserted-technical-profile.md) ou une [action de contrôle d’affichage](display-controls.md#display-control-actions), les métadonnées du profil technique auto-déclaré **UserMessageIfPhoneNumberParseFailure** contrôlent le message d’erreur présenté à l’utilisateur.

![Diagramme du chemin d’exécution du message d’erreur](./media/phone-authentication/assert-execution.png)

Vous pouvez utiliser cette transformation de revendications pour fractionner un numéro de téléphone complet en indicatif de pays et numéro national. Si le numéro de téléphone fourni n’est pas valide, vous pouvez choisir de lever un message d’erreur.

L’exemple suivant tente de fractionner le numéro de téléphone en numéro national et indicatif de pays. Si le numéro de téléphone est valide, il est remplacé par le numéro national. Si le numéro de téléphone n’est pas valide, aucune exception n’est levée et le numéro de téléphone conserve sa valeur d’origine.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Le profil technique auto-déclaré qui appelle le profil technique de validation contenant cette transformation de revendications peut définir le message d’erreur.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Exemple 1

- Revendications d’entrée :
  - **phoneNumber** : +49 (123) 456-7890
- Paramètres d’entrée :
  - **throwExceptionOnFailure** : false
  - **countryCodeType** : ISO3166
- Revendications de sortie :
  - **nationalNumber** : 1234567890
  - **countryCode** : DE

### <a name="example-2"></a>Exemple 2

- Revendications d’entrée :
  - **phoneNumber** : +49 (123) 456-7890
- Paramètres d'entrée
  - **throwExceptionOnFailure** : false
  - **countryCodeType** : CallingCode
- Revendications de sortie :
  - **nationalNumber** : 1234567890
  - **countryCode** : +49
