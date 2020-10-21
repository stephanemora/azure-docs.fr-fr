---
title: Déployer des ressources dans un groupe d’administration
description: Décrit comment déployer des ressources au niveau du groupe d’administration dans un modèle Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 23f86d7d0b7e1f882cf3fb74adc484e0fe47db87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372423"
---
# <a name="create-resources-at-the-management-group-level"></a>Créer des ressources au niveau du groupe d’administration

À mesure que votre organisation arrive mûrit, vous pouvez déployer un modèle Azure Resource Manager (modèle ARM) pour créer des ressources au niveau du groupe d’administration. Par exemple, vous pouvez être amené à définir et attribuer des [stratégies](../../governance/policy/overview.md) ou un [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md) pour un groupe d’administration. Avec des modèles au niveau du groupe d’administration, vous pouvez de façon déclarative appliquer des stratégies et assigner des rôles au niveau du groupe d’administration.

## <a name="supported-resources"></a>Ressources prises en charge

Certains types de ressources ne peuvent pas être déployés au niveau du groupe d’administration. Cette section répertorie les types de ressources pris en charge.

Pour Azure Blueprints, utilisez :

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Pour les stratégies Azure, utilisez :

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Pour le contrôle d’accès en fonction du rôle Azure (Azure RBAC), utilisez :

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Pour les modèles imbriqués qui sont déployés sur des abonnements ou des groupes de ressources, utilisez :

* [deployments](/azure/templates/microsoft.resources/deployments)

Pour gérer vos ressources, utilisez :

* [balises](/azure/templates/microsoft.resources/tags)

## <a name="schema"></a>schéma

Le schéma que vous utilisez pour les déploiements au niveau du groupe d'administration est différent de celui utilisé pour les déploiements de groupes de ressources.

Pour les modèles, utilisez :

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Le schéma d’un fichier de paramètres est le même pour toutes les étendues de déploiement. Fichiers de fichiers de paramètres, utilisez :

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Étendues de déploiement

Lors du déploiement sur un groupe d’administration, vous pouvez cibler le groupe d’administration spécifié dans la commande de déploiement, ou sélectionner un autre groupe d’administration dans le locataire.

Les ressources définies dans la section des ressources du modèle sont appliquées au groupe d’administration à partir de la commande de déploiement.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

Pour cibler un autre groupe d’administration, ajoutez un déploiement imbriqué et spécifiez la propriété `scope`. Définissez la propriété `scope` sur une valeur au format `Microsoft.Management/managementGroups/<mg-name>`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,22":::

Vous pouvez également cibler des abonnements ou des groupes de ressources au sein d’un groupe d’administration. L’utilisateur qui déploie le modèle doit avoir accès à l’étendue spécifiée.

Pour cibler un abonnement au sein du groupe d’administration, utilisez un déploiement imbriqué et la propriété `subscriptionId`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="10,18":::

Pour cibler un groupe de ressources dans cet abonnement, ajoutez un autre déploiement imbriqué et la propriété `resourceGroup`.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="10,21,25":::

Pour utiliser un déploiement de groupe d’administration afin de créer un groupe de ressources dans un abonnement et de déployer un compte de stockage dans ce groupe de ressources, consultez [Déployer dans un abonnement et un groupe de ressources](#deploy-to-subscription-and-resource-group).

## <a name="deployment-commands"></a>Commandes de déploiement

Les commandes utilisées pour les déploiements de groupes d’administration sont différentes de celles utilisées pour les déploiements de groupes de ressources.

Pour Azure CLI, utilisez [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create) :

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Pour Azure PowerShell, utilisez [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Pour l’API REST, utilisez [Déploiements - Créer au niveau du groupe d’administration](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Emplacement et nom du déploiement

Pour les déploiements au niveau du groupe d'administration, vous devez fournir un emplacement de déploiement. L’emplacement du déploiement est distinct de l’emplacement des ressources que vous déployez. L’emplacement de déploiement indique où stocker les données de déploiement.

Vous pouvez fournir un nom de déploiement ou utiliser le nom de déploiement par défaut. Le nom par défaut est le nom du fichier de modèle. Par exemple, le déploiement d’un modèle nommé **azuredeploy.json** crée le nom de déploiement par défaut **azuredeploy**.

Pour chaque nom de déploiement, l’emplacement est immuable. Il n’est pas possible de créer un déploiement dans un emplacement s’il existe un déploiement du même nom dans un autre emplacement. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

## <a name="use-template-functions"></a>Utiliser des fonctions de modèle

Pour les déploiements au niveau du groupe d'administration, il existe quelques considérations importantes liées à l’utilisation des fonctions de modèle :

* La fonction [resourceGroup()](template-functions-resource.md#resourcegroup)**n’est pas** prise en charge.
* La fonction [subscription()](template-functions-resource.md#subscription) n’est **pas** prise en charge.
* Les fonctions [reference()](template-functions-resource.md#reference) et [list()](template-functions-resource.md#list) sont prises en charge.
* N’utilisez pas la fonction [resourceId()](template-functions-resource.md#resourceid) pour les ressources déployées sur le groupe d’administration.

  Utilisez plutôt la fonction [extensionResourceId()](template-functions-resource.md#extensionresourceid) pour les ressources qui sont implémentées en tant qu’extensions du groupe d’administration. Les définitions de stratégie personnalisée qui sont déployées sur un groupe d’administration sont des extensions de celui-ci.

  Pour obtenir l’ID de ressource d’une définition de stratégie personnalisée au niveau du groupe d’administration, utilisez :
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Utilisez la fonction [tenantResourceId](template-functions-resource.md#tenantresourceid) pour les ressources de locataire qui sont disponibles dans le groupe d’administration. Les définitions de stratégie intégrées sont des ressources de niveau locataire.

  Pour obtenir l’ID de ressource d’une définition de stratégie intégrée, utilisez :
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

L’exemple suivant montre comment [définir](../../governance/policy/concepts/definition-structure.md) une stratégie au niveau du groupe d’administration et l’attribuer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Déployer dans un abonnement et un groupe de ressources

À partir d’un déploiement au niveau du groupe d’administration, vous pouvez cibler un abonnement au sein du groupe d’administration. L’exemple suivant crée un groupe de ressources à l’intérieur d’un abonnement et déploie un compte de stockage sur ce groupe de ressources.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’attribution de rôles, consultez [Ajouter des attributions de rôle Azure à l’aide de modèles Resource Manager](../../role-based-access-control/role-assignments-template.md).
* Pour obtenir un exemple de déploiement des paramètres d’espace de travail pour Azure Security Center, consultez [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Vous pouvez également déployer des modèles au [niveau de l’abonnement](deploy-to-subscription.md) et au [niveau du locataire](deploy-to-tenant.md).
