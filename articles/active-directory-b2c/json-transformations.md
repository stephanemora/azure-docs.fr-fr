---
title: Exemples de transformation de revendications JSON pour les stratégies personnalisées
titleSuffix: Azure AD B2C
description: Exemples de transformation de revendications JSON pour le schéma IEF (Identity Experience Framework) d’Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 56c46b8f2804e37544c94ec2d6ced7e8879b1ffa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75367125"
---
# <a name="json-claims-transformations"></a>Transformations de revendications JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article fournit des exemples pour l’utilisation de transformations de revendications JSON du schéma Identity Experience Framework dans Azure Active Directory B2C (Azure AD B2C). Pour plus d’informations, voir [ClaimsTransformations](claimstransformations.md).

## <a name="generatejson"></a>GenerateJson

Utilisez des valeurs de revendication ou de constantes pour générer une chaîne JSON. La chaîne de chemin d’accès suivant la notation par points est utilisée pour indiquer où insérer les données dans une chaîne JSON. Une fois le fractionnement par points effectué, tous les entiers sont interprétés comme l’index d’un tableau JSON et les valeurs non entières sont interprétées comme l’index d’un objet JSON.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Toute chaîne suivant la notation par points | string | JsonPath du JSON dans lequel la valeur de revendication sera insérée. |
| InputParameter | Toute chaîne suivant la notation par points | string | JsonPath du JSON dans lequel la valeur de chaîne de constante sera insérée. |
| OutputClaim | outputClaim | string | Chaîne JSON générée. |

L’exemple suivant génère une chaîne JSON en fonction de la valeur de revendication « email » et « otp », ainsi que de chaînes de constante.

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

La transformation des revendications suivante génère une revendication de chaîne JSON qui constituera le corps de la requête envoyée à SendGrid (un fournisseur de messagerie tiers). La structure de l'objet JSON est définie par les ID en notation par points des éléments InputParameters et des éléments TransformationClaimTypes de InputClaims. Les nombres dans la notation par points impliquent des tableaux. Les valeurs proviennent des valeurs InputClaims et des propriétés « Value » des éléments InputParameters.

- Revendications d’entrée :
  - **email**,  transformation claim type  **personalizations.0.to.0.email**: "someone@example.com"
  - **otp**, transformation claim type **personalizations.0.dynamic_template_data.otp** "346349"
- Paramètre d’entrée :
  - **template_id**: "d-4c56ffb40fa648b1aa6822283df94f60"
  - **from.email**: "service@contoso.com"
  - **personalizations.0.subject** "Contoso account email verification code"
- Revendication de sortie :
  - **requestBody** : Valeur JSON

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obtient un élément spécifié à partir de données JSON.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | ClaimTypes qui sont utilisés par la transformation de revendication pour obtenir l’élément. |
| InputParameter | claimToExtract | string | Nom de l’élément JSON à extraire. |
| OutputClaim | extractedClaim | string | ClaimType généré après l’appel de cette transformation de revendication, la valeur de l’élément spécifiée dans le paramètre d’entrée _claimToExtract_. |

Dans l’exemple suivant, la transformation de revendication a extrait l’élément `emailAddress` à partir des données JSON : `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Paramètre d’entrée :
    - **claimToExtract** : emailAddress
- Revendications de sortie :
  - **extractedClaim** : someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obtenir une liste d’éléments spécifiés à partir de données Json.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | ClaimTypes utilisés par la transformation de revendication pour obtenir les revendications. |
| InputParameter | errorOnMissingClaims | boolean | Spécifie s’il faut lever une erreur si l’une des revendications est manquante. |
| InputParameter | includeEmptyClaims | string | Spécifiez s’il faut inclure les revendications vides. |
| InputParameter | jsonSourceKeyName | string | Nom de clé d’élément |
| InputParameter | jsonSourceValueName | string | Nom de valeur d’élément |
| OutputClaim | Collection | string, int, boolean et datetime |Liste de revendications à extraire. Le nom de la revendication doit être égal à celui spécifié dans la revendication d’entrée _jsonSourceClaim_. |

Dans l’exemple suivant, la transformation de revendication extrait les revendications suivantes : email (string), displayName (string), membershipNum (int), active (boolean) et birthdate (datetime) à partir des données JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- Revendications d’entrée :
  - **jsonSourceClaim** : [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Paramètres d’entrée :
    - **errorOnMissingClaims** : false
    - **includeEmptyClaims** : false
    - **jsonSourceKeyName** : key
    - **jsonSourceValueName** : value
- Revendications de sortie :
  - **email** : "someone@example.com"
  - **displayName** : "Someone"
  - **membershipNum** : 6353399
  - **active** : true
  - **birthdate** : 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtient un élément numérique (long) spécifié à partir de données JSON.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | ClaimTypes utilisés par la transformation de revendication pour obtenir les revendications. |
| InputParameter | claimToExtract | string | Nom de l’élément JSON à extraire. |
| OutputClaim | extractedClaim | long | ClaimType généré après l’appel de cette ClaimsTransformation, la valeur de l’élément spécifiée dans les paramètres d’entrée _claimToExtract_. |

Dans l’exemple suivant, la transformation de revendication extrait l’élément `id` à partir des données JSON.

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Paramètres d'entrée
    - **claimToExtract** :  id
- Revendications de sortie :
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtient le premier élément à partir d’un tableau de données JSON.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | ClaimTypes qui sont utilisés par la transformation de revendication pour obtenir l’élément à partir du tableau JSON. |
| OutputClaim | extractedClaim | string | ClaimType généré après l’appel de cette ClaimsTransformation, le premier élément du tableau JSON. |

Dans l’exemple suivant, la transformation de revendication extrait le premier élément (adresse e-mail) à partir du tableau JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemple

- Revendications d’entrée :
  - **inputJsonClaim** : ["someone@example.com", "Someone", 6353399]
- Revendications de sortie :
  - **extractedClaim** : someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Convertit des données XML au format JSON.

| Élément | TransformationClaimType | Type de données | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | string | ClaimTypes qui sont utilisés par la transformation de revendication pour convertir les données XML au format JSON. |
| OutputClaim | json | string | ClaimType généré après l’appel de cette ClaimsTransformation, les données au format JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

Dans l’exemple suivant, la transformation de revendication convertit les données XML suivantes au format JSON.

#### <a name="example"></a>Exemple
Revendication d’entrée :

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Revendication de sortie :

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```
