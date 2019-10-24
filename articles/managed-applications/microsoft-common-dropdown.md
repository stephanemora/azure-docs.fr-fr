---
title: Élément de l’interface utilisateur DropDown | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.DropDown pour le Portail Azure. Permet de sélectionner les options disponibles lors du déploiement d’une application managée.
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
ms.openlocfilehash: 5784b5df9b522f0489ca1f6087627b45cec5349d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331739"
---
# <a name="microsoftcommondropdown-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.DropDown
Contrôle de sélection avec liste déroulante.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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
- Si elle est spécifiée, la valeur par défaut doit être une étiquette présente dans `constraints.allowedValues`. Dans le cas contraire, le premier élément de `constraints.allowedValues` est sélectionné. La valeur par défaut est **null**.
- `constraints.allowedValues` doit avoir au moins un élément.
- Pour émuler une valeur qui n’est pas requise, ajoutez un élément avec une étiquette et la valeur de `""` (chaîne vide) à `constraints.allowedValues`.

## <a name="sample-output"></a>Exemple de sortie
```json
"two"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
