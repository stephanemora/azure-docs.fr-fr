---
title: Gérer des groupes de ressources - Azure PowerShell
description: Utilisez Azure PowerShell pour gérer vos groupes de ressources dans Azure Resource Manager. Montre comment créer, lister et supprimer des groupes de ressources.
author: mumian
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c3bb028186155cc3af47f8efb293b7dbe61e13c9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731049"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gérer des groupes de ressources Azure Resource Manager à l'aide d'Azure PowerShell

Apprenez à utiliser Azure PowerShell avec [Azure Resource Manager](overview.md) pour gérer vos groupes de ressources Azure. Pour gérer des ressources Azure, consultez [Gérer les ressources Azure à l'aide d'Azure PowerShell](manage-resources-powershell.md).

Autres articles sur la gestion des groupes de ressources :

- [Gérer les groupes de ressources Azure à l’aide du portail Azure](manage-resources-portal.md)
- [Gérer les groupes de ressources Azure à l'aide d'Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Qu’est-ce qu’un groupe de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment ajouter des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. En règle générale, il convient d’ajouter des ressources qui partagent le même cycle de vie dans un même groupe de ressources afin de pouvoir facilement les déployer, les mettre à jour et les supprimer en tant que groupe.

Le groupe de ressources stocke des métadonnées sur les ressources. Lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

## <a name="create-resource-groups"></a>Créer des groupes de ressources

Pour créer un groupe de ressources, utilisez [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name exampleGroup -Location westus
```

## <a name="list-resource-groups"></a>Répertorier les groupes de ressources

Pour répertorier les groupes de ressources de votre abonnement, utilisez la commande [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell-interactive
Get-AzResourceGroup
```

Pour obtenir un groupe de ressources spécifique, indiquez son nom.

```azurepowershell-interactive
Get-AzResourceGroup -Name exampleGroup
```

## <a name="delete-resource-groups"></a>Supprimer des groupes de ressources

Pour supprimer un groupe de ressources, utilisez la commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name exampleGroup
```

Pour plus d’informations sur l’ordre dans lequel Azure Resource Manager supprime des ressources, voir [Suppression d’un groupe de ressources par Azure Resource Manager](delete-resource-group.md).

## <a name="deploy-resources"></a>Déployer des ressources

Vous pouvez déployer des ressources Azure à l’aide d’Azure PowerShell ou en déployant un modèle ARM (Azure Resource Manager) ou un fichier Bicep.

L’exemple suivant permet de créer un compte de stockage. Le nom que vous fournissez pour le compte de stockage doit être unique dans Azure.

```azurepowershell-interactive
New-AzStorageAccount -ResourceGroupName exampleGroup -Name examplestore -Location westus -SkuName "Standard_LRS"
```

Pour déployer un modèle ARM ou un fichier Bicep, utilisez la commande [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment).

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile storage.bicep
```

Pour des informations complètes sur le déploiement d’un modèle ARM, consultez [Déployer des ressources à l’aide de modèles ARM et d’Azure PowerShell](../templates/deploy-powershell.md).

Pour plus d'informations sur le déploiement d’un fichier Bicep, consultez [Déployer des ressources à l’aide de Bicep et d’Azure PowerShell](../bicep/deploy-powershell.md).

## <a name="lock-resource-groups"></a>Verrouiller des groupes de ressources

Le verrouillage empêche les utilisateurs de votre organisation de supprimer ou de modifier accidentellement des ressources critiques. 

Pour empêcher la suppression d’un groupe de ressources et de ses ressources, utilisez la commande [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleGroup
```

Pour obtenir les verrous d’un groupe de ressources, utilisez la commande [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock).

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleGroup
```

Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Baliser des groupes de ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exporter des groupes de ressources dans des modèles

Pour aider à créer des modèles ARM, vous pouvez exporter un modèle à partir de ressources existantes. Pour plus d’informations, consultez [Utiliser Azure PowerShell pour exporter un modèle](../templates/export-template-powershell.md). 

## <a name="manage-access-to-resource-groups"></a>Gérer l'accès aux groupes de ressources

Le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/overview.md) vous permet de gérer l’accès aux ressources dans Azure. Pour plus d’informations, consultez [Ajouter ou supprimer des attributions de rôle Azure à l’aide d’Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](overview.md).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](../templates/syntax.md).
