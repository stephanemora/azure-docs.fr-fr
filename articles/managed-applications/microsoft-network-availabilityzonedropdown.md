---
title: Élément de l’interface utilisateur AvailabilityZoneDropdown | Microsoft Docs
description: Décrit l’élément d’interface utilisateur Microsoft.Network.AvailabilityZoneDropdown pour le portail Azure.
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: 03156f2fd763bb7f2b1d2ba86d042f43f268febe
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftnetworkavailabilityzonedropdown-ui-element"></a>Élément d’interface utilisateur Microsoft.Network.AvailabilityZoneDropdown
Contrôle de sélection de la zone de disponibilité.

## <a name="ui-sample"></a>Exemple d’interface utilisateur
![Microsoft.Network.AvailabilityZoneDropDown](./media/managed-application-elements/microsoft.network.availabilityzonedropdown.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Network.AvailabilityZoneDropdown",
  "label": "Availability zone dropdown label",
  "toolTip": "This is the tooltip for availability zones",
  "defaultValue": "None",
  "constraints": {
    "required": "true"
  },
  "options": {
  },
  "visible": true
}
```

## <a name="next-steps"></a>Étapes suivantes
* Pour voir une présentation de la création de définitions d’interface utilisateur, consultez la page [Prise en main de CreateUiDefinition](create-uidefinition-overview.md).
* Pour obtenir une description des propriétés communes des éléments d’interface utilisateur, consultez la page [Éléments de CreateUiDefinition](create-uidefinition-elements.md).
