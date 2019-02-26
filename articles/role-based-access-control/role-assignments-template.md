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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 537ee35e96a41cd02605319e244d39c6567c3bf1
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337201"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gérer l’accès aux ressources Azure avec RBAC et les modèles Azure Resource Manager

Le [contrôle d’accès en fonction du rôle (RBAC)](overview.md) vous permet de gérer l’accès aux ressources Azure. En plus d’utiliser Azure PowerShell ou Azure CLI, vous pouvez gérer l’accès aux ressources Azure à l’aide de RBAC et des [modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Les modèles peuvent être utiles si vous devez déployer les ressources de manière cohérente et répétée. Cet article décrit comment vous pouvez gérer l’accès à l’aide de RBAC et des modèles.

## <a name="example-template-to-create-a-role-assignment"></a>Exemple de modèle pour créer une attribution de rôle

Dans le contrôle d’accès en fonction du rôle, vous créez une attribution de rôle pour accorder l’accès. Le modèle suivant montre comment :
- Attribuer un rôle à un utilisateur, un groupe ou une application dans l’étendue d’un groupe de ressources
- Spécifier les rôles Propriétaire, Collaborateur et Lecteur comme paramètre

Pour utiliser le modèle, vous devez spécifier les entrées suivantes :
- Nom d’un groupe de ressources
- Identificateur unique d’un utilisateur, d’un groupe ou d’une application auquel ou à laquelle attribuer le rôle
- Rôle à attribuer
- Identificateur unique à utiliser pour l’attribution de rôle

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
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

Voici un exemple d’attribution d’un rôle Lecteur à un utilisateur après avoir déployé le modèle.

![Attribution de rôle à l’aide d’un modèle](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Déployer le modèle à l’aide d’Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Pour déployer le modèle précédent à l’aide d’Azure PowerShell, effectuez les étapes suivantes.

1. Créez un fichier nommé rbac-rg.json et copiez le modèle précédent.

1. Connectez-vous à [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Obtenez l’identificateur unique d’un utilisateur, d’un groupe ou d’une application. Par exemple, vous pouvez utiliser la commande [Get-AzADUser](/powershell/module/az.resources/get-azaduser) pour dresser la liste des utilisateurs Azure AD.

    ```azurepowershell
    Get-AzADUser
    ```

1. Utilisez un outil GUID pour générer un identificateur unique qui servira pour l’attribution de rôle. Cet identificateur est au format : `11111111-1111-1111-1111-111111111111`.

1. Créez un exemple de groupe de ressources.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Utilisez la commande [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) pour démarrer le déploiement.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    Vous êtes invité à spécifier les paramètres requis. Voici un exemple de sortie.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
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

## <a name="deploy-template-using-the-azure-cli"></a>Déployer le modèle à l’aide d’Azure CLI

Pour déployer le modèle précédent à l’aide d’Azure CLI, effectuez les étapes suivantes.

1. Créez un fichier nommé rbac-rg.json et copiez le modèle précédent.

1. Connectez-vous à [Azure CLI](/cli/azure/authenticate-azure-cli).

1. Obtenez l’identificateur unique d’un utilisateur, d’un groupe ou d’une application. Par exemple, vous pouvez utiliser la commande [az ad user list](/cli/azure/ad/user#az-ad-user-list) pour répertorier les utilisateurs Azure AD.

    ```azurecli
    az ad user list
    ```

1. Utilisez un outil GUID pour générer un identificateur unique qui servira pour l’attribution de rôle. Cet identificateur est au format : `11111111-1111-1111-1111-111111111111`.

1. Créez un exemple de groupe de ressources.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Utilisez la commande [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) pour démarrer le déploiement.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    Vous êtes invité à spécifier les paramètres requis. Voici un exemple de sortie.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
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
    
## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
