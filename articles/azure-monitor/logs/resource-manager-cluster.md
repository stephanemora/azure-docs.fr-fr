---
title: Exemples de modèles Resource Manager pour les clusters Log Analytics
description: Exemples de modèles Azure Resource Manager pour déployer des clusters Log Analytics.
ms.topic: sample
author: yossiy
ms.author: yossiy
ms.date: 09/12/2021
ms.openlocfilehash: 1e585db0e7e0cec05319c74419bb840200d1d86a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659227"
---
# <a name="resource-manager-template-samples-for-log-analytics-clusters-in-azure-monitor"></a>Exemples de modèles Resource Manager pour les clusters Log Analytics dans Azure Monitor
Cet article contient des exemples de [modèles Azure Resource Manager](../../azure-resource-manager/templates/syntax.md) pour créer et configurer des clusters Log Analytics dans Azure Monitor. Chaque exemple comprend un fichier de modèle et un fichier de paramètres avec des exemples de valeurs à fournir au modèle.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>Références du modèle

- [Clusters Microsoft.OperationalInsights](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/clusters) 

## <a name="create-a-log-analytics-cluster"></a>Créer un cluster Log Analytics
L’exemple suivant crée un cluster Log Analytics vide.

### <a name="template-file"></a>Fichier de modèle

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "CommitmentTier": {
      "type": "int",
      "allowedValues": [
        500,
        1000,
        2000,
        5000
      ],
      "defaultValue": 500,
      "metadata": {
        "description": "The Capacity Reservation value."
      }
  },
  "billingType": {
      "type": "string",
      "allowedValues": [
        "Cluster",
        "Workspaces"
      ],
      "defaultValue": "Cluster",
      "metadata": {
          "description": "The billing type settings. Can be 'Cluster' (default) or 'Workspaces' for proportional billing on workspaces."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "CapacityReservation",
        "capacity": "[parameters('CommitmentTier')]"
      },
      "properties":  {
        "billingType": "[parameters('billingType')]"
      }
    }
  ]
}
```

### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "CommitmentTier": {
      "value": 500
    },
    "billingType": {
      "value": "Cluster"
    }
  }
}
```

## <a name="update-a-log-analytics-cluster"></a>Mettre à jour un cluster Log Analytics
L’exemple suivant met à jour un cluster Log Analytics pour utiliser une clé gérée par le client.

### <a name="template-file"></a>Fichier de modèle

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Log Analytics cluster."
      }
    },
    "keyVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The key identifier URI."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The key name."
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "The key version. When empty, latest key version is used."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.OperationalInsights/clusters",
      "apiVersion": "2021-06-01",
      "location": "[resourceGroup().location]",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties":  {
        "keyVaultProperties": {
        "keyVaultUri": "https://key-vault-name.vault.azure.net",
        "keyName": "key-name",
        "keyVersion": "current-version"
        }
      }  
    }
  ]
}
```

### <a name="parameter-file"></a>Fichier de paramètres

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "MyCluster"
    },
    "keyVaultUri": {
      "value": "https://key-vault-name.vault.azure.net"
    },
    "keyName": {
      "value": "MyKeyName"
    },
    "keyVersion": {
      "value": ""
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir d’autres exemples de modèle pour Azure Monitor](../resource-manager-samples.md).
* [En savoir plus sur les clusters dédiés Log Analytics](./logs-dedicated-clusters.md).
* [En savoir plus sur les sources de données d’agent](../agents/agent-data-sources.md).
