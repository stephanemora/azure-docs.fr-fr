---
title: Gérer les ressources Azure à l’aide d’Azure PowerShell | Microsoft Docs
description: Utilisez Azure PowerShell et Azure Resource Manager pour gérer vos ressources.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 8e6c4047182901c5282f280f59fa95eca6571ecc
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417919"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Gérer les ressources Azure à l’aide d’Azure PowerShell

Découvrez comment utiliser Azure PowerShell avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos ressources Azure. Pour la gestion des groupes de ressources, consultez [des groupes de ressources Azure de gérer à l’aide d’Azure PowerShell](./manage-resource-groups-powershell.md).

Autres articles sur la gestion des ressources :

- [Gérer les ressources Azure à l’aide du portail Azure](./manage-resources-portal.md)
- [Gérer les ressources Azure à l’aide d’Azure CLI](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Déployer des ressources à un groupe de ressources existant

Vous pouvez déployer des ressources Azure directement à l’aide d’Azure PowerShell, ou déployer un modèle Resource Manager pour créer des ressources Azure.

### <a name="deploy-a-resource"></a>Déployer une ressource

Le script suivant crée un compte de stockage.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Déploiement d’un modèle

Le script suivant crée déployer un modèle de démarrage rapide pour créer un compte de stockage. Pour plus d’informations, consultez [Démarrage rapide : Créer des modèles Azure Resource Manager à l’aide de Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](./resource-group-template-deploy.md).

## <a name="deploy-a-resource-group-and-resources"></a>Déployer un groupe de ressources et les ressources

Vous pouvez créer un groupe de ressources et déployer des ressources dans le groupe. Pour plus d'informations, consultez [Créer un groupe de ressources et déployer des ressources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Déployer des ressources sur plusieurs abonnements ou groupes de ressources

En général, vous déployez toutes les ressources dans votre modèle sur un seul groupe de ressources. Toutefois, il existe des scénarios dans lesquels vous pouvez souhaitez déployer un ensemble de ressources, tout en les plaçant dans différents groupes de ressources ou abonnements. Pour plus d’informations, consultez [déployer des ressources Azure à plusieurs abonnements ou groupes de ressources](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Supprimer des ressources

Le script suivant montre comment supprimer un compte de stockage.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Pour plus d’informations sur la façon dont Azure Resource Manager trie la suppression des ressources, consultez [suppression du groupe de ressources Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Déplacer des ressources

Le script suivant montre comment supprimer un compte de stockage à partir d’un groupe de ressources vers un autre groupe de ressources.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Pour suivre un tutoriel, consultez [Tutoriel : Déplacer des ressources Azure vers un autre groupe de ressources ou un autre abonnement](./resource-manager-tutorial-move-resources.md). 

Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](resource-group-move-resources.md).

## <a name="lock-resources"></a>Verrouiller des ressources

Le verrouillage empêche les autres utilisateurs de votre organisation de supprimer ou modifier des ressources critiques, telles que l’abonnement Azure, groupe de ressources ou ressource accidentellement. 

Le script suivant verrouille un compte de stockage pour le compte ne peut pas être supprimé.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Le script suivant obtient tous les verrous pour un compte de stockage :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Le script suivant supprime un verrou d’un compte de stockage :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Baliser des ressources

Balisage vous aide à organiser votre groupe de ressources et les ressources logiquement. Pour plus d’informations, consultez [à l’aide de balises pour organiser vos ressources Azure](./resource-group-using-tags.md#powershell).

## <a name="manage-access-to-resources"></a>Gérer l'accès aux ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d’informations, consultez [gérer l’accès à l’aide de RBAC et Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’Azure Resource Manager, consultez [présentation d’Azure Resource Manager](./resource-group-overview.md).
- Pour découvrir la syntaxe du modèle Resource Manager, consultez [comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour savoir comment développer des modèles, consultez le [didacticiels pas à pas](/azure/azure-resource-manager/).
- Pour afficher les schémas de modèle Azure Resource Manager, consultez [référence de modèle](/azure/templates/).
