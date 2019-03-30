---
title: Gérer des groupes d’Azure Resource Manager à l’aide d’Azure PowerShell | Microsoft Docs
description: Utiliser Azure PowerShell pour gérer vos groupes d’Azure Resource Manager.
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
ms.openlocfilehash: 8ae86d8bc7914a7a9c41eee93bb16b2f774993b9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651782"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gérer des groupes de ressources Azure Resource Manager à l’aide d’Azure PowerShell

Découvrez comment utiliser Azure PowerShell avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos groupes de ressources Azure. Pour la gestion des ressources Azure, consultez [gérer les ressources Azure à l’aide d’Azure PowerShell](./manage-resources-powershell.md).

Autres articles sur la gestion des groupes de ressources :

- [Gérer des groupes de ressources Azure à l’aide du portail Azure](./manage-resources-portal.md)
- [Gérer des groupes de ressources Azure à l’aide d’Azure CLI](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Qu'est-ce qu'un groupe de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. En règle générale, il convient d’ajouter des ressources qui partagent le même cycle de vie dans un même groupe de ressources afin de pouvoir facilement les déployer, les mettre à jour et les supprimer en tant que groupe.

Le groupe de ressources stocke des métadonnées sur les ressources. Par conséquent, lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

Le groupe de ressources stocke des métadonnées sur les ressources. Lorsque vous spécifiez un emplacement pour le groupe de ressources, vous spécifiez où se trouve que les métadonnées.

## <a name="create-resource-groups"></a>Créer des groupes de ressources

Le script PowerShell suivant crée un groupe de ressources et montre ensuite le groupe de ressources.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Répertorier les groupes de ressources

Le script PowerShell suivant répertorie les groupes de ressources sous votre abonnement.

```azurepowershell-interactive
Get-AzResourceGroup
```

Pour obtenir un groupe de ressources :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Supprimer des groupes de ressources

Le script PowerShell suivant supprime un groupe de ressources :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Pour plus d’informations sur la façon dont Azure Resource Manager trie la suppression des ressources, consultez [suppression du groupe de ressources Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Déployer des ressources à un groupe de ressources existant

Consultez [déployer des ressources à un groupe de ressources existant](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Pour valider un déploiement de groupe de ressources, consultez [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Déployer un groupe de ressources et les ressources

Vous pouvez créer un groupe de ressources et déployer des ressources dans le groupe à l’aide d’un modèle Resource Manager. Pour plus d'informations, consultez [Créer un groupe de ressources et déployer des ressources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Redéploiement en cas d’échec du déploiement

Cette fonctionnalité est également appelé *restauration en cas d’erreur*. Pour plus d’informations, consultez [redéployer en cas d’échec du déploiement](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Déplacer vers un autre groupe de ressources ou abonnement

Vous pouvez déplacer les ressources dans le groupe vers un autre groupe de ressources. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](./resource-group-move-resources.md#move-resources).

## <a name="lock-resource-groups"></a>Groupes de ressources de verrouillage

Le verrouillage empêche les autres utilisateurs de votre organisation de supprimer ou modifier des ressources critiques, telles que l’abonnement Azure, groupe de ressources ou ressource accidentellement. 

Le script suivant verrouille un groupe de ressources pour le groupe de ressources ne peut pas être supprimé.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Le script suivant obtient tous les verrous pour un groupe de ressources :

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Baliser des groupes de ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations, consultez [à l’aide de balises pour organiser vos ressources Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exporter les groupes de ressources à des modèles

Après avoir correctement configuré votre groupe de ressources, vous souhaiterez afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

- Automatiser les déploiements futurs de la solution, car le modèle contient toute l’infrastructure complète.
- Découvrez la syntaxe de modèle en regardant dans la Notation JSON (JavaScript Object) qui représente votre solution.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Pour plus d’informations, consultez [exporter un groupe de ressources](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Gérer l’accès aux groupes de ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d’informations, consultez [gérer l’accès à l’aide de RBAC et Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’Azure Resource Manager, consultez [présentation d’Azure Resource Manager](./resource-group-overview.md).
- Pour découvrir la syntaxe du modèle Resource Manager, consultez [comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour savoir comment développer des modèles, consultez le [didacticiels pas à pas](/azure/azure-resource-manager/).
- Pour afficher les schémas de modèle Azure Resource Manager, consultez [référence de modèle](/azure/templates/).