---
title: Élément d’interface utilisateur TextBlock Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.TextBlock pour le portail Azure.
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
ms.openlocfilehash: 0dcd8ba1b1bacd990c91ce90ef0013caac446cdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251568"
---
# <a name="microsoftcommontextblock-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.TextBlock
Contrôle qui peut être utilisé pour ajouter du texte à l’interface du portail.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Exemple de sortie

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
