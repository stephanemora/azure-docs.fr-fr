---
title: Élément d'interface utilisateur TextBox
description: Décrit l’élément d’interface utilisateur Microsoft.Common.TextBox pour le Portail Azure. Permet d’ajouter du texte non mis en forme.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649757"
---
# <a name="microsoftcommontextbox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.TextBox

Contrôle pouvant être utilisé pour modifier du texte non mis en forme.

## <a name="ui-sample"></a>Exemple d’interface utilisateur

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>schéma

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exemple de sortie

```json
"my text value"
```

## <a name="remarks"></a>Notes

- Si `constraints.required` est défini sur **true**, la zone de texte doit contenir une valeur pour permettre la réussite de la validation. La valeur par défaut est **false**.
- `constraints.regex` est un modèle d’expression régulière JavaScript. S’il est spécifié, la valeur de la zone de texte doit correspondre au modèle pour permettre la réussite de la validation. La valeur par défaut est **null**.
- `constraints.validationMessage` est une chaîne à afficher en cas d’échec de la validation de la valeur de la zone de texte. Si elle n’est pas spécifiée, les messages de validation intégrés de la zone de texte sont utilisés. La valeur par défaut est **null**.
- Il est possible de spécifier une valeur pour `constraints.regex` lorsque `constraints.required` est défini sur **false**. Dans ce scénario, aucune valeur n’est requise dans la zone de texte pour que la validation réussisse. Si elle est spécifiée, celle-ci doit correspondre au modèle d’expression régulière.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
