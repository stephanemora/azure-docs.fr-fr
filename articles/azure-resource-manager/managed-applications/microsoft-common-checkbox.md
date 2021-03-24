---
title: Élément d’interface utilisateur CheckBox
description: Décrit l’élément d’interface utilisateur Microsoft.Common.CheckBox pour le Portail Azure. Permet aux utilisateurs d’activer ou de désactiver une option.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87094253"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.CheckBox

Le contrôle CheckBox permet aux utilisateurs de cocher ou de décocher une option. Le contrôle retourne **true** lorsque le contrôle est activé, ou **false** lorsqu’il n’est pas activé.

## <a name="ui-sample"></a>Exemple d’interface utilisateur

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft.Common.CheckBox":::

## <a name="schema"></a>schéma

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Exemple de sortie

```json
true
```

## <a name="remarks"></a>Notes

Lorsque vous définissez **requis** sur **true**, l’utilisateur doit activer la case à cocher. Si l’utilisateur ne sélectionne pas la case à cocher, le message de validation s’affiche.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
