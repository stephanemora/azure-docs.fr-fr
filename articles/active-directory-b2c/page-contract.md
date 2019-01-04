---
title: Sélectionner un contrat de page dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment sélectionner un contrat de page dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e2e6da5df434ffd217b0521d4a13cd8ec713d5a1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110781"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Sélectionner un contrat de page dans Azure Active Directory B2C à l’aide de stratégies personnalisées

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Vous pouvez sélectionner un contrat de page dans Azure Active Directory (Azure AD) B2C en le configurant dans une [stratégie personnalisée](active-directory-b2c-overview-custom.md). Un contrat de page désigne une association d’éléments qu’Azure AD B2C fournit et du contenu que vous fournissez. Si vous prévoyez d’utiliser [JavaScript](javascript-samples.md), vous devez définir une version de contrat de page pour l’ensemble de vos définitions de contenu dans votre stratégie personnalisée.

## <a name="replace-datauri-values"></a>Remplacer les valeurs DataUri

Dans vos stratégies personnalisées, [ContentDefinitions](contentdefinitions.md) peut définir les modèles HTML utilisés dans le parcours utilisateur. **ContentDefinition** contient un **DataUri** qui fait référence aux éléments de page fournis par Azure AD B2C. **LoadUri** désigne le chemin d’accès relatif au contenu HTML et CSS que vous fournissez.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

Pour sélectionner un contrat de page, vous modifiez les valeurs **DataUri** dans l’élément [ContentDefinitions](contentdefinitions.md) au sein de vos stratégies. En passant des anciennes valeurs **DataUri** aux nouvelles valeurs, vous sélectionnez un package immuable. Ce package présente l’avantage de ne jamais changer ni provoquer de comportement inattendu sur votre page. 

Pour définir un contrat de page, utilisez le tableau suivant afin de prendre connaissance des valeurs **DataUri**. 

| Ancienne valeur DataUri | Nouvelle valeur DataUri |
| ----------------- | ----------------- |
| urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment personnaliser l’interface utilisateur de vos applications, consultez [Personnaliser l’interface utilisateur de votre application à l’aide d’une stratégie personnalisée dans Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



