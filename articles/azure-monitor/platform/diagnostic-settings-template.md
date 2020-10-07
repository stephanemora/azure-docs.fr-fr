---
title: Créer un paramètre de diagnostic dans Azure à l’aide du modèle Resource Manager
description: Créez des paramètres de diagnostic à l’aide d’un modèle Resource Manager pour transférer les journaux de la plateforme Azure vers les journaux Azure Monitor, le Stockage Azure ou Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 73f2b3ea90cc94fa3411552c7b812fe53eb4dbbb
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607234"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>Créer un paramètre de diagnostic dans Azure à l’aide d’un modèle Resource Manager
Dans Azure Monitor, les [paramètres de diagnostic](diagnostic-settings.md) indiquent où envoyer les [journaux de plateforme](platform-logs-overview.md) collectés par les ressources et la plateforme Azure dont ils dépendent. Cet article fournit des détails et des exemples sur l’utilisation d’un [modèle Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) dans le cadre de la création et de la configuration de paramètres de diagnostic pour collecter les journaux de plateforme vers différentes destinations.

> [!NOTE]
> Étant donné que vous ne pouvez pas [créer de paramètre de diagnostic](diagnostic-settings.md) pour le journal d’activité Azure avec PowerShell ou CLI comme les paramètres de diagnostic pour les autres ressources Azure, créez un modèle Resource Manager pour le journal d’activité à l’aide des informations contenues dans cet article et déployez ce modèle avec PowerShell ou CLI.

## <a name="deployment-methods"></a>Méthodes de déploiement
Vous pouvez déployer des modèles Resource Manager à l’aide de n’importe quelle méthode valide, y compris PowerShell et CLI. Les paramètres de diagnostic du journal d’activité doivent être déployés sur un abonnement avec `az deployment create` pour CLI ou `New-AzDeployment` pour PowerShell. Les paramètres de diagnostic des journaux de ressources doivent être déployés sur un groupe de ressources avec `az group deployment create` pour CLI ou `New-AzResourceGroupDeployment` pour PowerShell.

Pour plus d'informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) et [Déployer des ressources à l’aide de modèles Resource Manager et d'Azure CLI](../../azure-resource-manager/templates/deploy-cli.md). 





## <a name="resource-logs"></a>Journaux d’activité de ressources
Pour les journaux de ressources, ajoutez une ressource de type `<resource namespace>/providers/diagnosticSettings` au modèle. La section Propriétés obéit au format décrit dans [Paramètres de diagnostic - Créer ou mettre à jour](/rest/api/monitor/diagnosticsettings/createorupdate). Fournissez un `category` dans la section `logs` pour chacune des catégories valides correspondant à la ressource que vous souhaitez collecter. Ajoutez la propriété `metrics` pour collecter des mesures de ressources sur les mêmes destinations si la [ressource prend en charge les métriques](metrics-supported.md).

Voici un modèle collectant une catégorie de journal de ressources pour une ressource donnée vers un espace de travail Log Analytics, un compte de stockage et un Event Hub.

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>Exemple
Voici un exemple créant un paramètre de diagnostic pour un paramètre de mise à l’échelle automatique qui active la diffusion en continu des journaux de ressources vers un Event Hub, un compte de stockage et un espace de travail Log Analytics.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>Journal d’activité
Pour le journal d’activité Azure, ajoutez une ressource de type `Microsoft.Insights/diagnosticSettings`. Les catégories disponibles sont répertoriées dans [Catégories du journal d’activité](./activity-log.md#view-the-activity-log). Voici un modèle collectant toutes les catégories du journal d’activité vers un espace de travail Log Analytics, un compte de stockage et un Event Hub.


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>Étapes suivantes
* Apprenez-en davantage sur les [journaux de la plateforme Azure](platform-logs-overview.md).
* Découvrez les [paramètres de diagnostic](diagnostic-settings.md).
