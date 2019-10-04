---
title: Créer un groupe de ressources et des ressources au niveau abonnement - Modèle Azure Resource Manager
description: Décrit comment créer un groupe de ressources dans un modèle Azure Resource Manager. Est également expliqué le déploiement des ressources sur l’étendue de l’abonnement Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: tomfitz
ms.openlocfilehash: 37f2b04a62d94cce42b095540380460c38bc5b79
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772951"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>Créer des groupes de ressources et des ressources au niveau de l’abonnement

En règle générale, vous déployez des ressources Azure sur un groupe de ressources dans votre abonnement Azure. Toutefois, vous pouvez également créer des groupes de ressources Azure et créer des ressources Azure au niveau de l’abonnement. Pour déployer des modèles au niveau de l’abonnement, vous utilisez Azure CLI et Azure PowerShell. Le portail Azure ne prend pas en charge le déploiement dans le niveau de l’abonnement.

Pour créer un groupe de ressources dans un modèle Azure Resource Manager, définissez une ressource [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) avec un nom et un emplacement pour le groupe de ressources. Vous pouvez créer un groupe de ressources et déployer des ressources sur ce groupe de ressources dans le même modèle. Les ressources que vous pouvez déployer au niveau de l’abonnement sont les suivantes : [Stratégies](../governance/policy/overview.md) et [Contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md).

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

Les aspects suivants sont différents entre le déploiement au niveau de l’abonnement et le déploiement de groupe de ressources :

### <a name="schema-and-commands"></a>Schéma et commandes

Le schéma et les commandes que vous utilisez pour les déploiements au niveau abonnement sont différents de ceux utilisés pour les déploiements de groupes de ressources. 

Pour le schéma, utilisez `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`.

Pour la commande de déploiement Azure CLI, utilisez [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create). Par exemple, la commande CLI suivante déploie un modèle pour créer un groupe de ressources :

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

Pour la commande de déploiement PowerShell, utilisez [New-AzDeployment](/powershell/module/az.resources/new-azdeployment). Par exemple, la commande PowerShell suivante déploie un modèle pour créer un groupe de ressources :

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

### <a name="deployment-name-and-location"></a>Nom et emplacement du déploiement

Quand vous effectuez un déploiement sur votre abonnement, vous devez fournir un emplacement pour ce déploiement. Vous pouvez également fournir un nom pour le déploiement. Si vous ne spécifiez pas de nom pour le déploiement, le nom du modèle est utilisé comme nom de déploiement. Par exemple, le déploiement d’un modèle nommé **azuredeploy.json** crée le nom de déploiement par défaut **azuredeploy**.

L’emplacement des déploiements au niveau de l’abonnement est immuable. Vous ne pouvez pas créer un déploiement dans un emplacement alors qu’il existe un déploiement portant le même nom, mais à un autre emplacement. Si vous obtenez le code d’erreur `InvalidDeploymentLocation`, utilisez un autre nom ou le même emplacement que le déploiement précédent pour ce nom.

### <a name="use-template-functions"></a>Utiliser des fonctions de modèle

Pour les déploiements au niveau de l’abonnement, il existe quelques considérations importantes liées à l’utilisation des fonctions de modèle :

* La fonction [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) **n’est pas** prise en charge.
* La fonction [resourceId()](resource-group-template-functions-resource.md#resourceid) est prise en charge. Elle permet d’obtenir l’ID des ressources qui sont utilisées dans les déploiements au niveau de l’abonnement. Par exemple, vous pouvez obtenir l’ID de ressource d’une définition de stratégie avec `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`.
* Les fonctions [reference()](resource-group-template-functions-resource.md#reference) et [list()](resource-group-template-functions-resource.md#list) sont prises en charge.

## <a name="create-resource-groups"></a>Créer des groupes de ressources

Le modèle suivant crée un groupe de ressources vide.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Vous trouverez le schéma de modèle [ici](/azure/templates/microsoft.resources/allversions). Vous trouverez des modèles similaires dans [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments).

## <a name="create-multiple-resource-groups"></a>Créer plusieurs groupes de ressources

Pour créer plus d’un groupe de ressources, utilisez l’[élément copy](resource-group-create-multiple.md) avec des groupes de ressources. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Pour plus d’informations sur l’itération de ressource, consultez [Déployer plusieurs instances d’une ressource ou d’une propriété dans des modèles Azure Resource Manager](./resource-group-create-multiple.md) et [Didacticiel : Créer plusieurs instances de ressources grâce à des modèles Resource Manager](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="create-resource-group-and-deploy-resources"></a>Créer un groupe de ressources et déployer des ressources

Pour créer le groupe de ressources et déployer des ressources sur celui-ci, utilisez un modèle imbriqué. Le modèle imbriqué définit les ressources à déployer sur le groupe de ressources. Définissez le modèle imbriqué comme dépendant du groupe de ressources pour vous assurer que le groupe de ressources existe avant de déployer les ressources.

L’exemple suivant crée un groupe de ressources, et déploie un compte de stockage sur le groupe de ressources.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="create-policies"></a>Création des stratégies

### <a name="assign-policy"></a>Attribuer la stratégie

L’exemple suivant assigne une définition de stratégie existante à l’abonnement. Si la stratégie utilise des paramètres, fournissez-les en tant qu’objet. Si la stratégie n’utilise pas de paramètres, utilisez l’objet vide par défaut.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Pour déployer cet exemple de modèle avec Azure CLI, utilisez la commande suivante :

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Pour déployer ce modèle avec PowerShell, utilisez :

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>Définir et assigner une stratégie

Vous pouvez [définir](../governance/policy/concepts/definition-structure.md) et assigner une stratégie dans le même modèle.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
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
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Pour créer la définition de stratégie dans votre abonnement et l’appliquer à l’abonnement, utilisez la commande CLI suivante :

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Pour déployer ce modèle avec PowerShell, utilisez :

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur l’attribution de rôles, voir [Gérer l’accès aux ressources Azure avec RBAC et les modèles Azure Resource Manager](../role-based-access-control/role-assignments-template.md).
* Pour obtenir un exemple de déploiement des paramètres d’espace de travail pour Azure Security Center, consultez [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Création de modèles](resource-group-authoring-templates.md). 
* Pour obtenir la liste des fonctions disponibles dans un modèle, consultez [Fonctions de modèle](resource-group-template-functions.md).
