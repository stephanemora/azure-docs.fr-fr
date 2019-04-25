---
title: Élément d’interface utilisateur InfoBox Azure | Microsoft Docs
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
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 2330197b4512dfdd72de3529145103b644594e25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252090"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.InfoBox
Contrôle qui ajoute une zone d’information. La zone contient du texte important ou des avertissements qui aident les utilisateurs à comprendre les valeurs qu’ils fournissent. Elle peut également comporter un lien vers une URI proposant des informations supplémentaires.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schéma
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Remarques

* Pour `icon`, utilisez **Aucune**, **Info**, **Avertissement** ou **Erreur**.
* La propriété `uri` est facultative.

## <a name="sample-output"></a>Exemple de sortie

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
