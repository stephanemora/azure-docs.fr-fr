---
title: Élément d’interface utilisateur InfoBox Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.InfoBox pour le Portail Azure. Permet d’ajouter du texte ou des avertissements lors du déploiement d’une application managée.
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
ms.openlocfilehash: 67ae05c2ec7b36d76e49f26d5765bbc68b952292
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331687"
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
