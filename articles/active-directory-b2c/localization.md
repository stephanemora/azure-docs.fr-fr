---
title: Localisation - Azure Active Directory B2C
description: Spécifiez l’élément Localization d’une stratégie personnalisée dans Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126760"
---
# <a name="localization"></a>Localisation

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L’élément **Localization** vous permet de prendre en charge plusieurs paramètres régionaux ou langues dans la stratégie pour les parcours utilisateur. La prise en charge de la localisation dans les stratégies vous permet d’effectuer les actions suivantes :

- Configurer la liste explicite des langues prises en charge dans une stratégie et choisir une langue par défaut.
- Fournir des collections et des chaînes spécifiques d’une langue.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

L’élément **Localization** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| activé | Non | Valeurs possibles : `true` ou `false`. |

L’élément **Localization** contient les éléments XML suivants

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | Liste des langues prises en charge. |
| LocalizedResources | 0:n | Liste des ressources localisées. |

## <a name="supportedlanguages"></a>SupportedLanguages

L’élément **SupportedLanguages** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| DefaultLanguage | Oui | Langue à utiliser par défaut pour les ressources localisées. |
| MergeBehavior | Non | Énumération de valeurs fusionnées avec un élément ClaimType quelconque présent dans une stratégie parente avec le même identificateur. Utilisez cet attribut quand vous remplacez une revendication spécifiée dans la stratégie de base. Valeurs possibles : `Append`, `Prepend` ou `ReplaceAll`. La valeur `Append` spécifie que la collection de données présente doit être ajoutée à la fin de la collection spécifiée dans la stratégie parente. La valeur `Prepend` spécifie que la collection de données présente doit être ajoutée devant la collection spécifiée dans la stratégie parente. La valeur `ReplaceAll` spécifie que la collecte de données définie dans la stratégie parente doit être ignorée, et que les données définies dans la stratégie actuelle doivent être utilisées à la place. |

### <a name="supportedlanguages"></a>SupportedLanguages

L’élément **SupportedLanguages** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Affiche le contenu conforme à une balise de langue en vertu de la spécification RFC 5646 - Tags pour l’identification les langues. |

## <a name="localizedresources"></a>LocalizedResources

L’élément **LocalizedResources** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Id | Oui | Identificateur servant à identifier de façon unique les ressources localisées. |

L’élément **LocalizedResources** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Définit des collections entières dans des cultures différentes. Une collection peut contenir un nombre différent d’éléments, et des chaînes différentes pour diverses cultures. Les exemples de collections incluent les énumérations qui apparaissent dans les types de revendications. Par exemple, une liste de pays/régions s’affiche à l’utilisateur dans une liste déroulante. |
| LocalizedStrings | 0:n | Définit toutes les chaînes, à l’exception de celles qui apparaissent dans des collections, dans diverses cultures. |

### <a name="localizedcollections"></a>LocalizedCollections

Le **LocalizedCollections** élément contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | Liste des langues prises en charge. |

#### <a name="localizedcollection"></a>LocalizedCollection

L’élément **LocalizedCollections** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ElementType | Oui | Fait référence à un élément ClaimType ou à un élément d’interface utilisateur dans le fichier de stratégie. |
| ElementId | Oui | Chaîne contenant une référence à un type de revendication déjà défini dans la section ClaimsSchema, qui est utilisée si **ElementType** est défini sur ClaimType. |
| TargetCollection | Oui | Collection cible. |

L’élément **LocalizedCollections** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| Élément | 0:n | Définit une option que l’utilisateur peut sélectionner pour une revendication dans l’interface utilisateur, telle qu’une valeur dans une liste déroulante. |

L’élément **Item** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| Texte | Oui | Chaîne d’affichage convivial à présenter à l’utilisateur dans l’interface utilisateur pour cette option. |
| Valeur | Oui | Valeur de revendication chaîne associée à la sélection de cette option. |
| SelectByDefault | Non | Indique si cette option doit être sélectionnée par défaut dans l’interface utilisateur. Valeurs possibles : True ou False. |

L’exemple suivant illustre l’utilisation de l’élément **LocalizedCollections**. Il contient deux éléments **LocalizedCollection**, l’un pour l’anglais et un autre pour l’espagnol. Tous deux définissent la collection **Restriction** de la revendication `Gender` avec une liste d’éléments pour l’anglais et l’espagnol.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

L’élément **LocalizedStrings** contient les éléments suivants :

| Élément | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | Chaîne localisée. |

L’élément **LocalizedString** contient les attributs suivants :

| Attribut | Obligatoire | Description |
| --------- | -------- | ----------- |
| ElementType | Oui | Référence à un élément type de revendication ou à un élément interface utilisateur dans la stratégie. Valeurs possibles : `ClaimType`, `UxElement`, `ErrorMessage`, `Predicate` ou `GetLocalizedStringsTransformationClaimType`. La valeur `ClaimType` est utilisée pour localiser l’un des attributs de revendication, comme spécifié dans StringId. La valeur `UxElement` est utilisée pour localiser l’un des éléments d’interface utilisateur, comme spécifié dans StringId. La valeur `ErrorMessage` est utilisée pour localiser l’un des messages d’erreur système, comme spécifié dans StringId. La valeur `Predicate` est utilisée pour localiser l’un des messages d’erreur [Predicate](predicates.md), comme spécifié dans StringId. La valeur `InputValidation` est utilisée pour localiser l’un des messages d’erreur du groupe [PredicateValidation](predicates.md), comme spécifié dans StringId. La valeur `GetLocalizedStringsTransformationClaimType` est utilisée pour copier les chaînes localisées dans des revendications. Pour plus d’informations, consultez [Transformation de revendications GetLocalizedStringsTransformation](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | Oui | Si **ElementType** a la valeur `ClaimType`, `Predicate`, ou `InputValidation`, cet élément contient une référence à un type de revendication déjà défini dans la section ClaimsSchema. |
| StringId | Oui | Si **ElementType** a la valeur `ClaimType`, cet élément contient une référence à un attribut d’un type de revendication. Valeurs possibles : `DisplayName`, `AdminHelpText` ou `PatternHelpText`. La valeur `DisplayName` est utilisée pour définir le nom d’affichage de la revendication. La valeur `AdminHelpText` est utilisée pour définir le nom du texte d’aide de l’utilisateur de la revendication. La valeur `PatternHelpText` est utilisée pour définir le texte d’aide du modèle de revendication. Si **ElementType** a la valeur `UxElement`, cet élément contient une référence à un attribut d’un élément d’interface utilisateur. Si **ElementType** a la valeur `ErrorMessage`, cet élément spécifie l’identificateur d’un message d’erreur. Pour obtenir la liste complète des [ identificateurs, voir ](localization-string-ids.md)ID de chaîne de localisation`UxElement`.|


L’exemple suivant montre une page d’inscription localisée. Les trois premières valeurs **LocalizedString** définissent l’attribut de revendication. La troisième modifie la valeur du bouton Continuer. La dernière modifie le message d’erreur.

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

L’exemple suivant montre le texte **UserHelpText** localisé de **Predicate** avec l’Id `IsLengthBetween8And64`. Et un texte **UserHelpText** localisée de **PredicateGroup** avec l’Id `CharacterClasses` de **PredicateValidation** avec l’Id `StrongPassword`.

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>Configurer la localisation

Cet article montre comment prendre en charge plusieurs paramètres régionaux ou langues dans la stratégie des parcours utilisateur. La localisation nécessite trois étapes : configurer la liste explicite des langues prises en charge, fournir des chaînes et collections spécifiques de la langue, et modifier la ContentDefinition de la page.

### <a name="set-up-the-explicit-list-of-supported-languages"></a>Configurer la liste explicite des langues prises en charge

Sous l’élément **BuildingBlocks**, ajoutez l’élément **Localization** avec la liste des langues prises en charge. L’exemple suivant montre comment définir la prise en charge de la localisation pour l’anglais (par défaut) et l’espagnol :

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>Étapes suivantes

Pour voir des exemples de localisation, consultez les articles suivants :

- [Personnalisation de la langue avec la stratégie personnalisée dans Azure Active Directory B2C](custom-policy-localization.md)
- [Personnalisation de la langue avec les flux d’utilisateurs dans Azure Active Directory B2C](user-flow-language-customization.md)
