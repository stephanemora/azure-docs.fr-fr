---
title: Élément de l’interface utilisateur Section Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.Section pour le portail Azure.
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
ms.openlocfilehash: 90ffae3dd8b05041c34d766e464eb68f793f6066
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062976"
---
# <a name="microsoftcommonsection-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.Section
Contrôle qui regroupe un ou plusieurs éléments sous un titre.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- `elements` doit avoir au moins un élément et peut avoir tous les types d’éléments à l’exception de `Microsoft.Common.Section`.
- Cet élément ne prend pas en charge la propriété `toolTip`.

## <a name="sample-output"></a>Exemple de sortie
Pour accéder aux valeurs de sortie des éléments de `elements`, utilisez les fonctions [basics()](create-uidefinition-functions.md#basics) ou [steps()](create-uidefinition-functions.md#steps) et la notation sous forme de points :

```json
steps('configuration').section1.text1
```

Les éléments de type `Microsoft.Common.Section` n’ont eux-mêmes aucune valeur de sortie.

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
