---
title: Sélectionnez un contrat de page - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment sélectionner un contrat de page dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7aab43695f0b11590d8bd2aa011073ba04d95250
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512991"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Sélectionner un contrat de page dans Azure Active Directory B2C à l’aide de stratégies personnalisées

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Vous pouvez activer le code JavaScript côté client dans vos stratégies Azure Active Directory (Azure AD) B2C si vous utilisez des flux d’utilisateurs ou des stratégies personnalisées. Pour activer JavaScript pour vos applications, vous devez ajouter un élément à votre [stratégie personnalisée](active-directory-b2c-overview-custom.md), sélectionnez une page de contrat et utiliser [b2clogin.com](b2clogin.md) dans vos requêtes. Un contrat de page désigne une association d’éléments qu’Azure AD B2C fournit et du contenu que vous fournissez. Cet article décrit comment sélectionner un contrat de page dans Azure AD B2C en le configurant dans une stratégie personnalisée.

> [!NOTE]
> Si vous souhaitez activer JavaScript pour les flux d’utilisateurs, consultez [JavaScript et page de contrat de versions dans Azure Active Directory B2C](user-flow-javascript-overview.md).

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



