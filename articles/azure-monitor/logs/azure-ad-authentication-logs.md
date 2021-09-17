---
title: Authentification Azure AD pour Journaux
description: Découvrez comment activer l’authentification du Répertoire actif Azure (Azure AD) pour Log Analytics dans Azure Monitor.
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 526312df5c1bbf5e21a4017177752fb4f476ae5c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825290"
---
# <a name="azure-ad-authentication-for-logs"></a>Authentification Azure AD pour Journaux

Azure Monitor peut [collecter des données dans des Journaux provenant de plusieurs sources](data-platform-logs.md#data-collection), notamment des agents sur des machines virtuelles, des Insights d’application, des paramètres de diagnostic pour les ressources Azure et l’API du Collecteur de données.

Les agents Log Analytics utilisent une clé d’espace de travail comme clé d’inscription pour vérifier l’accès initial et fournir un certificat qui sera utilisé pour établir une connexion sécurisée entre l’agent et Azure Monitor. Pour en savoir plus, consultez [envoyer des données à partir d’agents](data-security.md#2-send-data-from-agents). L’API du Collecteur de données utilise la même clé d’espace de travail pour[autoriser l’accès](data-collector-api.md#authorization).

Ces options peuvent être fastidieuses et présenter un risque, car il est difficile de gérer les informations d’identification, notamment les clés de l’espace de travail à grande échelle. Vous pouvez choisir d’annuler l’authentification locale et de vous assurer que seules les données de télémétrie authentifiées exclusivement à l’aide d’Identités managées et du Répertoire actif Azure sont ingérées dans Azure Monitor. Cette fonctionnalité améliore la sécurité et la fiabilité des données de télémétrie utilisées pour prendre des décisions opérationnelles et commerciales critiques.

Utilisez les étapes suivantes pour activer l’intégration du répertoire actif Azure pour les journaux Azure Monitor et supprimer la confiance sur ces secrets partagés.

1. L’agent Azure Monitor (AMA) n’a besoin d’aucune clé, mais nécessite une [identité managée affectée par le système](../agents/azure-monitor-agent-overview.md#security). [Migrez vers l’Agent Azure Monitor ](../agents/azure-monitor-agent-migration.md) à partir des agents Log Analytics.
2. [Désactivez l’authentification locale pour les Espaces de travail Log Analytics](#disable-local-authentication-for-log-analytics).
3. Assurez-vous que seules les données de télémétrie authentifiées sont ingérées dans vos ressources d’Insights d’application avec l’[authentification Azure AD pour les Insights d’application (version préliminaire)](../app/azure-ad-authentication.md).

## <a name="disable-local-authentication-for-log-analytics"></a>Désactivez l’authentification locale pour Log Analytics

Après avoir supprimé votre dépendance sur l’agent Log Analytics, vous pouvez choisir de désactiver l’authentification locale pour les espaces de travail Log Analytics. Cela vous permettra d’ingérer et d’interroger uniquement les données de télémétrie authentifiées par Azure AD.

La désactivation de l’authentification locale peut limiter certaines fonctionnalités disponibles, notamment :

- Les agents Log Analytics existants cesseront de fonctionner, seul l’Agent Azure Monitor (AMA) est pris en charge. L’Agent Azure Monitor ne dispose pas de certaines fonctionnalités qui sont disponibles par le biais de l’agent Log Analytics (par exemple, la collecte de journaux personnalisée et la collecte de journaux IIS).
- L’API du Collecteur de données (préversion) ne prend pas en charge l’authentification Azure AD et ne sera pas disponible pour l’ingestion des données.

Vous pouvez désactiver l’authentification locale à l’aide de la Stratégie Azure ou par programme via le modèle du Gestionnaire de ressources Azure, PowerShell ou CLI.

### <a name="azure-policy"></a>Azure Policy

La Stratégie Azure pour « DisableLocalAuth » refusera aux utilisateurs de créer un nouvel Espace de travail Log Analytics sans affecter la valeur « true » à ce paramètre de propriété. Le nom de la stratégie est `Log Analytics Workspaces should block non-Azure Active Directory based ingestion`. Pour appliquer cette définition de stratégie à votre abonnement, [créez une nouvelle affectation de stratégie, puis affectez la stratégie](../../governance/policy/assign-policy-portal.md). 

Voici la définition du modèle de stratégie :

```json
{
  "properties": {
    "displayName": "Log Analytics Workspaces should block non-Azure Active Directory based ingestion.",
    "policyType": "BuiltIn",
    "mode": "Indexed",
    "description": "Enforcing log ingestion to require Azure Active Directory authentication prevents unauthenticated logs from an attacker which could lead to incorrect status, false alerts, and incorrect logs stored in the system.",
    "metadata": {
      "version": "1.0.0",
      "category": "Monitoring"
    },
    "parameters": {
      "effect": {
        "type": "String",
        "metadata": {
          "displayName": "Effect",
          "description": "Enable or disable the execution of the policy"
        },
        "allowedValues": [
          "Deny",
          "Audit",
          "Disabled"
        ],
        "defaultValue": "Audit"
      }
    },
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.OperationalInsights/workspaces"
          },
          {
            "field": "Microsoft.OperationalInsights/workspaces/features.disableLocalAuth",
            "notEquals": "true"
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
  },
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e15effd4-2278-4c65-a0da-4d6f6d1890e2",
  "type": "Microsoft.Authorization/policyDefinitions",
  "name": "e15effd4-2278-4c65-a0da-4d6f6d1890e2"
}
```

### <a name="azure-resource-manager"></a>Azure Resource Manager

La propriété `DisableLocalAuth` est utilisée pour désactiver toute authentification locale sur votre Espace de travail Log Analytics. Lorsque la valeur est définie sur « true », cette propriété impose que l’authentification Azure ad soit utilisée pour tous les accès. 

Voici un exemple de modèle du Gestionnaire de ressources Azure que vous pouvez utiliser pour désactiver l’authentification locale :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaces_name": {
            "defaultValue": "workspace-name",
            "type": "string"
        },
        "workspace_location": {
          "defaultValue": "region-name",
          "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaces_name')]",
            "location": "[parameters('workspace_location')]",
            "properties": {
                 "sku": {
                    "name": "PerGB2018"
                },
                "retentionInDays": 30,
                "features": {
                    "disableLocalAuth": false,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
}

```


### <a name="cli"></a>Interface de ligne de commande

La propriété `DisableLocalAuth` est utilisée pour désactiver toute authentification locale sur votre Espace de travail Log Analytics. Lorsque la valeur est définie sur « true », cette propriété impose que l’authentification Azure ad soit utilisée pour tous les accès. 

Voici un exemple de commandes CLI que vous pouvez utiliser pour désactiver l’authentification locale :

```azurecli
    az resource update --ids "/subscriptions/[Your subscription ID]/resourcegroups/[Your resource group]/providers/microsoft.operationalinsights/workspaces/[Your workspace name]--api-version "2021-06-01" --set properties.features.disableLocalAuth=True
```

### <a name="powershell"></a>PowerShell

La propriété `DisableLocalAuth` est utilisée pour désactiver toute authentification locale sur votre Espace de travail Log Analytics. Lorsque la valeur est définie sur « true », cette propriété impose que l’authentification Azure ad soit utilisée pour tous les accès. 

Voici un exemple de commandes PowerShell que vous pouvez utiliser pour désactiver l’authentification locale :

```powershell
    $workspaceSubscriptionId = "[You subscription ID]"
    $workspaceResourceGroup = "[You resource group]"
    $workspaceName = "[Your workspace name]"
    $disableLocalAuth = $false
    
    # login
    Connect-AzAccount
    
    # select subscription
    Select-AzSubscription -SubscriptionId $workspaceSubscriptionId
    
    # get private link workspace resource
    $workspace = Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ResourceGroupName $workspaceResourceGroup -ResourceName $workspaceName -ApiVersion "2021-06-01"
    
    # set DisableLocalAuth
    $workspace.Properties.Features | Add-Member -MemberType NoteProperty -Name DisableLocalAuth -Value $disableLocalAuth -Force
    $workspace | Set-AzResource -Force
```

## <a name="next-steps"></a>Étapes suivantes
* [Authentification Azure AD pour les Insights d’application (préversion)](../app/azure-ad-authentication.md)