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
ms.date: 07/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e6511ff84c251577a5ff483f892387ab7d3d4d41
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360460"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gérer l’accès aux ressources Azure avec RBAC et les modèles Azure Resource Manager

Le [contrôle d’accès en fonction du rôle (RBAC)](overview.md) vous permet de gérer l’accès aux ressources Azure. En plus d’utiliser Azure PowerShell ou Azure CLI, vous pouvez gérer l’accès aux ressources Azure à l’aide de RBAC et des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Les modèles peuvent être utiles si vous devez déployer les ressources de manière cohérente et répétée. Cet article décrit comment vous pouvez gérer l’accès à l’aide de RBAC et des modèles.

## <a name="assign-role-to-resource-group-or-subscription"></a>Attribuer un rôle à un groupe de ressources ou à un abonnement

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès. Le modèle suivant montre comment :
- Comment attribuer un rôle à un utilisateur, un groupe ou une application dans un groupe de ressources ou la portée d’un abonnement
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

L’exemple suivant illustre une attribution de rôle lecteur à un utilisateur pour un groupe de ressources après le déploiement du modèle.

![Attribution de rôle à l’aide d’un modèle](./media/role-assignments-template/role-assignment-template.png)

L’étendue de l’attribution de rôle est déterminée à partir du niveau du déploiement. Les commandes de déploiement au niveau du groupe de ressources et de l’abonnement sont présentées dans cet article.

## <a name="assign-role-to-resource"></a>Attribuer un rôle à la ressource

Si vous avez besoin de créer une attribution de rôle au niveau d’une ressource, le format de l’attribution de rôle est différent. Vous fournissez l’espace de noms du fournisseur de ressources et le type de ressource de la ressource à laquelle attribuer le rôle. Vous devez également inclure le nom de la ressource dans le nom de l’attribution de rôle.

Pour le type et le nom de l’attribution de rôle, utilisez le format suivant :

```json
"type": "{resource-provider-namespace}/{resource-type}/providers/roleAssignments",
"name": "{resource-name}/Microsoft.Authorization/{role-assign-GUID}"
```

Le modèle suivant déploie un compte de stockage et lui attribue un rôle. Vous le déployez avec les commandes de groupe de ressources.

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

L’exemple suivant illustre une attribution de rôle contributeur à un utilisateur pour un compte de stockage après le déploiement du modèle.

![Attribution de rôle à l’aide d’un modèle](./media/role-assignments-template/role-assignment-template-resource.png)

## <a name="deploy-template-using-azure-powershell"></a>Déployer le modèle à l’aide d’Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Pour déployer le modèle précédent dans un groupe de ressources ou un abonnement à l’aide de Azure PowerShell, procédez comme suit.

1. Créez un fichier nommé rbac-rg.json et copiez le modèle précédent.

1. Connectez-vous à [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Obtenez l’identificateur unique d’un utilisateur, d’un groupe ou d’une application. Par exemple, vous pouvez utiliser la commande [Get-AzADUser](/powershell/module/az.resources/get-azaduser) pour dresser la liste des utilisateurs Azure AD.

    ```azurepowershell
    $userid = (Get-AzADUser -DisplayName "{name}").id
    ```

1. Le modèle génère une valeur par défaut pour le GUID utilisé pour identifier l’attribution de rôle. Si vous devez spécifier un GUID spécifique, transmettez cette valeur dans pour le paramètre roleNameGuid. Cet identificateur est au format : `11111111-1111-1111-1111-111111111111`.

Pour attribuer le rôle au niveau d’une ressource ou d’un groupe de ressources, procédez comme suit :

1. Créez un exemple de groupe de ressources.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Utilisez la commande [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) pour démarrer le déploiement.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    ```

    Voici un exemple de sortie.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

Pour attribuer le rôle au niveau d’un abonnement, utilisez la commande [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) et spécifiez un emplacement pour le déploiement.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-rg.json -principalId $userid -builtInRoleType Reader
```

Il présente une sortie similaire à la commande de déploiement pour les groupes de ressources.

## <a name="deploy-template-using-the-azure-cli"></a>Déployer le modèle à l’aide d’Azure CLI

Pour déployer le modèle précédent à l’aide de Azure CLI vers un groupe de ressources ou un abonnement, procédez comme suit.

1. Créez un fichier nommé rbac-rg.json et copiez le modèle précédent.

1. Connectez-vous à [Azure CLI](/cli/azure/authenticate-azure-cli).

1. Obtenez l’identificateur unique d’un utilisateur, d’un groupe ou d’une application. Par exemple, vous pouvez utiliser la commande [az ad user show](/cli/azure/ad/user#az-ad-user-show) pour afficher les utilisateurs Azure AD.

    ```azurecli
    userid=$(az ad user show --upn-or-object-id "{email}" --query objectId --output tsv)
    ```

1. Le modèle génère une valeur par défaut pour le GUID utilisé pour identifier l’attribution de rôle. Si vous devez spécifier un GUID spécifique, transmettez cette valeur dans pour le paramètre roleNameGuid. Cet identificateur est au format : `11111111-1111-1111-1111-111111111111`.

Pour attribuer le rôle au niveau d’une ressource ou d’un groupe de ressources, procédez comme suit :

1. Créez un exemple de groupe de ressources.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Utilisez la commande [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) pour démarrer le déploiement.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    ```

    Voici un exemple de sortie.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
    
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```

Pour attribuer le rôle au niveau d’un abonnement, utilisez la commande [az deployment create](/cli/azure/deployment#az-deployment-create) et spécifiez un emplacement pour le déploiement.

```azurecli
az deployment create --location centralus --template-file rbac-rg.json --parameters principalId=$userid builtInRoleType=Reader
```

Il présente une sortie similaire à la commande de déploiement pour les groupes de ressources.

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
