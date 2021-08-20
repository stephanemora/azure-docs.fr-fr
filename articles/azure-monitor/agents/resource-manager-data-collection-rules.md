---
title: Exemples de modèles Resource Manager pour les règles de collecte de données
description: Exemples de modèles Azure Resource Manager pour créer des associations entre les règles de collecte de données et les machines virtuelles dans Azure Monitor.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 24bc9b0f92a82354f511f534eb9b0e86d59d336b
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112201263"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Exemples de modèles Resource Manager pour les règles de collecte de données dans Azure Monitor
Cet article comprend des exemples de [modèles Azure Resource Manager](../../azure-resource-manager/templates/syntax.md) pour créer une association entre une [règle de collecte de données](data-collection-rule-overview.md) et l’[agent Azure Monitor](./azure-monitor-agent-overview.md). Chaque exemple comprend un fichier de modèle et un fichier de paramètres avec des exemples de valeurs à fournir au modèle.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-rule-sample"></a>Créer une règle (exemple)

Voir [Format de modèle](/azure/templates/microsoft.insights/datacollectionrules)

## <a name="create-association-with-azure-vm"></a>Créer une association avec une machine virtuelle Azure

L’exemple suivant crée une association entre une machine virtuelle Azure et une règle de collecte de données.

### <a name="template-file"></a>Fichier de modèle

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Créer une association avec Azure Arc

L’exemple suivant crée une association entre un serveur avec Azure Arc et une règle de collecte de données.

### <a name="template-file"></a>Fichier de modèle

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Étapes suivantes

* [Obtenir d’autres exemples de modèle pour Azure Monitor](../resource-manager-samples.md).
* [En savoir plus sur l’agent Log Analytics](./log-analytics-agent.md).
* [En savoir plus sur l’extension de diagnostic](./diagnostics-extension-overview.md).
