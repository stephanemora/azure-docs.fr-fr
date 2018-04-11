---
title: Élément d’interface utilisateur OptionsGroup Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.OptionsGroup pour le portail Azure.
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
ms.openlocfilehash: 4b2ce987e311a12e3833fcc1dbae2b48b40135ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.OptionsGroup
Contrôle de sélection avec une ligne d’options disponibles.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "my value",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ]
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- L’étiquette de `constraints.allowedValues` est le texte qui s’affiche pour un élément, et sa valeur est la valeur de sortie de l’élément sélectionné lors de la sélection.
- Si elle est spécifiée, la valeur par défaut doit être une étiquette présente dans `constraints.allowedValues`. Dans le cas contraire, le premier élément de `constraints.allowedValues` est sélectionné par défaut. La valeur par défaut est **null**.
- `constraints.allowedValues` doit contenir au moins un élément.
- Cet élément ne prend pas en charge la propriété `constraints.required` ; un élément doit être sélectionné pour réussir la validation.

## <a name="sample-output"></a>Exemple de sortie
```json
"Bar"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
