---
title: Élément d’interface utilisateur TextBox Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.TextBox pour le Portail Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: daee95c177ecddfea1cf7d6d162906fcc86938b4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommontextbox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.TextBox
Contrôle pouvant être utilisé pour modifier du texte non mis en forme.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- Si `constraints.required` est défini sur **true**, la zone de texte doit contenir une valeur permettant de réussir la validation. La valeur par défaut est **false**.
- `constraints.regex` est un modèle d’expression régulière JavaScript. S’il est spécifié, la valeur de la zone de texte doit correspondre au modèle pour permettre la réussite de la validation. La valeur par défaut est **null**.
- `constraints.validationMessage` est une chaîne à afficher en cas d’échec de la validation de la valeur de la zone de texte. Si elle n’est pas spécifiée, les messages de validation intégrés de la zone de texte sont utilisés. La valeur par défaut est **null**.
- Il est possible de spécifier une valeur pour `constraints.regex` lorsque `constraints.required` est défini sur **false**. Dans ce scénario, aucune valeur n’est requise pour valider la zone de texte. Si elle est spécifiée, celle-ci doit correspondre au modèle d’expression régulière.

## <a name="sample-output"></a>Exemple de sortie

```json
"foobar"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
