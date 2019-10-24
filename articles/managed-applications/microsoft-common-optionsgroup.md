---
title: Élément d’interface utilisateur OptionsGroup Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.OptionsGroup pour le portail Azure. Permet aux utilisateurs de sélectionner les options disponibles lors du déploiement d’une application managée.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 7bec506575f1c526e59487edb67349cdf862f1a7
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331666"
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
  "defaultValue": "Value two",
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
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- L’étiquette de `constraints.allowedValues` est le texte qui s’affiche pour un élément, et sa valeur est la valeur de sortie de l’élément sélectionné lors de la sélection.
- Si elle est spécifiée, la valeur par défaut doit être une étiquette présente dans `constraints.allowedValues`. Dans le cas contraire, le premier élément de `constraints.allowedValues` est sélectionné par défaut. La valeur par défaut est **null**.
- `constraints.allowedValues` doit avoir au moins un élément.

## <a name="sample-output"></a>Exemple de sortie
```json
"two"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
