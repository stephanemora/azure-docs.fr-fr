---
title: Gérer l’accès aux ressources Azure avec RBAC et les modèles Azure Resource Manager | Microsoft Docs
description: Découvrez comment gérer l’accès aux ressources Azure pour les utilisateurs, les groupes et les applications à l’aide du contrôle d’accès en fonction du rôle (RBAC) et des modèles Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b7f701cd3ce07099d80bca40e506108bcc9a9da9
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178101"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gérer l’accès aux ressources Azure avec RBAC et les modèles Azure Resource Manager

Le [contrôle d'accès en fonction du rôle (RBAC)](overview.md) vous permet de gérer l'accès aux ressources Azure. En plus d’utiliser Azure PowerShell ou Azure CLI, vous pouvez gérer l’accès aux ressources Azure à l’aide des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Les modèles peuvent être utiles si vous devez déployer les ressources de manière cohérente et répétée. Cet article décrit comment vous pouvez gérer l’accès à l’aide de RBAC et des modèles.

## <a name="create-a-role-assignment-at-a-resource-group-scope-without-parameters"></a>Créer une attribution de rôle dans une étendue de groupe de ressources (sans paramètres)

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès. Le modèle suivant montre comment créer une attribution de rôle de façon très simple. Certaines valeurs sont spécifiées dans le modèle. Le modèle suivant montre comment :

-  Attribuer un rôle [lecteur](built-in-roles.md#reader) à un utilisateur, un groupe ou une application dans une étendue de groupe de ressources

Pour utiliser le modèle, vous devez effectuer les opérations suivantes :

- Créez un fichier JSON et copiez le modèle.
- Remplacez `<your-principal-id>` par l’identificateur unique d’une application, d’un utilisateur ou d’un groupe auquel attribuer le rôle. Cet identificateur est au format : `11111111-1111-1111-1111-111111111111`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Les exemples suivants, avec les commandes [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) et [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), montrent comment démarrer le déploiement dans un groupe de ressources nommé ExampleGroup.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

L’exemple suivant illustre l’attribution du rôle lecteur à un utilisateur pour un groupe de ressources après le déploiement du modèle.

![Attribution de rôle dans une étendue de groupe de ressources](./media/role-assignments-template/role-assignment-template.png)

## <a name="create-a-role-assignment-at-a-resource-group-or-subscription-scope"></a>Créer une attribution de rôle dans une étendue de groupe de ressources ou d’abonnement

Le modèle précédent n’est pas très flexible. Le modèle suivant utilise des paramètres et peut s’appliquer à différentes étendues. Le modèle suivant montre comment :

- Attribuer un rôle à un utilisateur, un groupe ou une application dans une étendue de groupe de ressources ou d’abonnement
- Spécifier les rôles Propriétaire, Collaborateur et Lecteur comme paramètre

Pour utiliser le modèle, vous devez spécifier les entrées suivantes :

- Identificateur unique d’un utilisateur, d’un groupe ou d’une application auquel ou à laquelle attribuer le rôle
- Rôle à attribuer
- Un identificateur unique qui sera utilisé pour l’attribution de rôle, ou vous pouvez utiliser l’identificateur par défaut

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Pour obtenir l’identificateur unique de l’utilisateur auquel attribuer le rôle, vous pouvez utiliser la commande [Get-AzADUser](/powershell/module/az.resources/get-azaduser) ou [az ad user show](/cli/azure/ad/user#az-ad-user-show).

```azurepowershell
$userid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
```

L’étendue de l’attribution de rôle est déterminée à partir du niveau du déploiement. Les exemples suivants, avec les commandes [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) et [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), montrent comment démarrer le déploiement dans une étendue de groupe de ressources.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

Les exemples suivants, avec les commandes [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) et [az deployment create](/cli/azure/deployment#az-deployment-create), montrent comment démarrer le déploiement dans une étendue d’abonnement et spécifier l’emplacement.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Reader
```

## <a name="create-a-role-assignment-at-a-resource-scope"></a>Créer une attribution de rôle dans une étendue de ressource

Si vous avez besoin de créer une attribution de rôle au niveau d’une ressource, le format de l’attribution de rôle est différent. Vous fournissez l’espace de noms du fournisseur de ressources et le type de ressource de la ressource à laquelle attribuer le rôle. Vous devez également inclure le nom de la ressource dans le nom de l’attribution de rôle.

Pour le type et le nom de l’attribution de rôle, utilisez le format suivant :

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Le modèle suivant montre comment :

- Création d’un nouveau compte de stockage
- Attribuer un rôle à un utilisateur, un groupe ou une application dans l’étendue d’un compte de stockage
- Spécifier les rôles Propriétaire, Collaborateur et Lecteur comme paramètre

Pour utiliser le modèle, vous devez spécifier les entrées suivantes :

- Identificateur unique d’un utilisateur, d’un groupe ou d’une application auquel ou à laquelle attribuer le rôle
- Rôle à attribuer
- Un identificateur unique qui sera utilisé pour l’attribution de rôle, ou vous pouvez utiliser l’identificateur par défaut


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleNameGuid'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Pour déployer le modèle précédent, vous devez utiliser les commandes de groupe de ressources. Les exemples suivants, avec les commandes [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) et [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), montrent comment démarrer le déploiement dans une étendue de ressource.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $userid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$userid builtInRoleType=Contributor
```

L’exemple suivant illustre l’attribution du rôle contributeur à un utilisateur pour un compte de stockage après le déploiement du modèle.

![Attribution de rôle dans une étendue de ressource](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="create-a-role-assignment-for-a-new-service-principal"></a>Créer une attribution de rôle pour un nouveau principal de service

Dans certains cas, si vous créez un principal de service et que vous tentez immédiatement de lui attribuer un rôle, cette attribution peut échouer. Par exemple, si vous créez une identité managée et que vous tentez d’attribuer un rôle à ce principal de service dans le même modèle Azure Resource Manager, l’attribution de rôle peut échouer. Cet échec est souvent lié au délai de réplication. Le principal du service est créé dans une région. Toutefois, l’attribution de rôle peut s’effectuer dans une autre région, qui n’a pas encore répliqué le principal de service. Dans le cadre de ce scénario, vous devez définir la propriété `principalType` sur `ServicePrincipal` lors de la création de l’attribution de rôle.

Le modèle suivant montre comment :

- Créer un principal de service d’identité managée
- Spécifier le `principalType`
- Attribuer le rôle contributeur à ce principal de service dans une étendue de groupe de ressources

Pour utiliser le modèle, vous devez spécifier les entrées suivantes :

- Le nom de base de l’identité managée. Vous pouvez également utiliser la chaîne par défaut.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Les exemples suivants, avec les commandes [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) et [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create), montrent comment démarrer le déploiement dans une étendue de groupe de ressources.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

L’exemple suivant illustre l’attribution du rôle contributeur à un nouveau principal de service d’identité managée après le déploiement du modèle.

![Attribution de rôle pour un nouveau principal de service d’identité managée](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Créer des groupes de ressources et des ressources au niveau de l’abonnement](../azure-resource-manager/deploy-to-subscription.md)
- [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
