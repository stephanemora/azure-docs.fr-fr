---
title: Élément d’interface utilisateur PasswordBox Azure | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Common.PasswordBox pour le Portail Azure.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: eab4bea750c28ac3b156363f5f63d8c7c8f4075a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Élément d’interface utilisateur Microsoft.Common.PasswordBox
Contrôle qui peut être utilisé pour indiquer et confirmer un mot de passe.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Remarques
- Cet élément ne prend pas en charge la propriété `defaultValue`.
- Pour obtenir des détails relatifs à l’implémentation de `constraints`, consultez [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Si `options.hideConfirmation` est défini sur **true**, la deuxième zone de texte de confirmation du mot de passe utilisateur est masquée. La valeur par défaut est **false**.

## <a name="sample-output"></a>Exemple de sortie
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
