---
title: Ajouter des conditions d’attribution de rôle Azure avec des modèles Azure Resource Manager (préversion) - Azure ABAC
description: Découvrez comment ajouter des conditions de contrôle d’accès en fonction des attributs (ABAC) dans les attributions de rôle Azure à l’aide de modèles Azure Resource Manager et du contrôle d’accès en fonction du rôle Azure (RBAC Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 06/29/2021
ms.author: rolyon
ms.openlocfilehash: 6e64afaab3b367ed807f77e5ebc0214904ed763f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094823"
---
# <a name="add-azure-role-assignment-conditions-using-azure-resource-manager-templates-preview"></a>Ajouter des conditions d’attribution de rôle Azure avec des modèles Azure Resource Manager (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une [condition d’attribution de rôle Azure](conditions-overview.md) est une vérification supplémentaire que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès plus précis. Par exemple, vous pouvez ajouter une condition qui oblige un objet à porter une étiquette spécifique pour être lu. Cet article explique comment ajouter des conditions pour vos attributions de rôles à l’aide de modèles Azure Resource Manager.

## <a name="prerequisites"></a>Configuration requise

Pour plus d’informations sur les prérequis à l’ajout des conditions d’attribution de rôle, consultez [Prérequis aux conditions](conditions-prerequisites.md).

## <a name="add-a-condition"></a>Ajouter une condition

Le modèle suivant montre comment attribuer le rôle [Lecteur des données blob du stockage](built-in-roles.md#storage-blob-data-reader) avec une condition. La condition vérifie si le nom du conteneur correspond à « blobs-example-container ».

Pour utiliser le modèle, vous devez spécifier l’entrée suivante :

- L’ID d’un utilisateur, d’un groupe, d’une identité managée ou d’une application auxquels attribuer le rôle.
- Le type de principal, comme `User`, `Group` ou `ServicePrincipal`. Pour plus d’informations, consultez la page [Nouveau principal de service](role-assignments-template.md#new-service-principal).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "Principal ID to assign the role to"
            }
        },
        "principalType": {
            "type": "string",
            "metadata": {
                "description": "Type of principal"
            }
        },
        "roleAssignmentGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "New GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "StorageBlobDataReader": "[concat(subscription().Id, '/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1')]" // ID for Storage Blob Data Reader role, but can be any valid role ID
    },
    "resources": [
        {
            "name": "[parameters('roleAssignmentGuid')]",
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview", // API version to call the role assignment PUT.
            "properties": {
                "roleDefinitionId": "[variables('StorageBlobDataReader')]",
                "principalId": "[parameters('principalId')]",
                "principalType": "[parameters('principalType')]",
                "description": "Role assignment condition created with an ARM template",
                "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))", // Role assignment condition
                "conditionVersion": "2.0"
            }
        }
    ]
}
```

L’étendue de l’attribution de rôle est déterminée à partir du niveau du déploiement. Les exemples suivants, avec les commandes [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) et [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create), montrent comment démarrer le déploiement dans une étendue de groupe de ressources.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName example-group -TemplateFile rbac-test.json -principalId $principalId -principalType "User"
```

```azurecli
az deployment group create --resource-group example-group --template-file rbac-test.json --parameters principalId=$principalId principalType="User"
```

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Résoudre les problèmes liés aux conditions d’attribution de rôle Azure (préversion)](conditions-troubleshoot.md)
- [Attribuer des rôles Azure avec des modèles Azure Resource Manager](role-assignments-template.md)
