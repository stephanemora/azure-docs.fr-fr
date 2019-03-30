---
title: Gérer des groupes d’Azure Resource Manager à l’aide d’Azure CLI | Microsoft Docs
description: Utiliser Azure CLI pour gérer vos groupes d’Azure Resource Manager.
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
ms.openlocfilehash: 9245d4c01816d26e262b1c8bd35015e2a6a9558c
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650731"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gérer des groupes de ressources Azure Resource Manager à l’aide d’Azure CLI

Découvrez comment utiliser Azure CLI avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos groupes de ressources Azure. Pour la gestion des ressources Azure, consultez [gérer les ressources Azure à l’aide d’Azure CLI](./manage-resources-cli.md).

Autres articles sur la gestion des groupes de ressources :

- [Gérer des groupes de ressources Azure à l’aide du portail Azure](./manage-resources-portal.md)
- [Gérer des groupes de ressources Azure à l’aide d’Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Qu'est-ce qu'un groupe de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. En règle générale, il convient d’ajouter des ressources qui partagent le même cycle de vie dans un même groupe de ressources afin de pouvoir facilement les déployer, les mettre à jour et les supprimer en tant que groupe.

Le groupe de ressources stocke des métadonnées sur les ressources. Par conséquent, lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

Le groupe de ressources stocke des métadonnées sur les ressources. Lorsque vous spécifiez un emplacement pour le groupe de ressources, vous spécifiez où se trouve que les métadonnées.

## <a name="create-resource-groups"></a>Créer des groupes de ressources

Le script CLI suivant crée un groupe de ressources et montre ensuite le groupe de ressources.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Répertorier les groupes de ressources

Le script CLI suivant répertorie les groupes de ressources sous votre abonnement.

```azurecli-interactive
az group list
```

Pour obtenir un groupe de ressources :

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Supprimer des groupes de ressources

Le script CLI suivant supprime un groupe de ressources :

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Pour plus d’informations sur la façon dont Azure Resource Manager trie la suppression des ressources, consultez [suppression du groupe de ressources Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Déployer des ressources à un groupe de ressources existant

Consultez [déployer des ressources à un groupe de ressources existant](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Déployer un groupe de ressources et les ressources

Vous pouvez créer un groupe de ressources et déployer des ressources dans le groupe à l’aide d’un modèle Resource Manager. Pour plus d'informations, consultez [Créer un groupe de ressources et déployer des ressources](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Redéploiement en cas d’échec du déploiement

Cette fonctionnalité est également appelé *restauration en cas d’erreur*. Pour plus d’informations, consultez [redéployer en cas d’échec du déploiement](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Déplacer vers un autre groupe de ressources ou abonnement

Vous pouvez déplacer les ressources dans le groupe vers un autre groupe de ressources. Pour plus d’informations, consultez [déplacer des ressources](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Groupes de ressources de verrouillage

Le verrouillage empêche les autres utilisateurs de votre organisation de supprimer ou modifier des ressources critiques, telles que l’abonnement Azure, groupe de ressources ou ressource accidentellement. 

Le script suivant verrouille un groupe de ressources pour le groupe de ressources ne peut pas être supprimé.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Le script suivant obtient tous les verrous pour un groupe de ressources :

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Le script suivant supprime un verrou :

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Baliser des groupes de ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations, consultez [à l’aide de balises pour organiser vos ressources Azure](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exporter les groupes de ressources à des modèles

Après avoir correctement configuré votre groupe de ressources, vous souhaiterez afficher le modèle Resource Manager pour le groupe de ressources. L’exportation du modèle offre deux avantages :

- Automatiser les déploiements futurs de la solution, car le modèle contient toute l’infrastructure complète.
- Découvrez la syntaxe de modèle en regardant dans la Notation JSON (JavaScript Object) qui représente votre solution.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Le script affiche le modèle sur la console.  Copiez le JSON et enregistrez-le dans un fichier.

Pour plus d’informations, consultez [exporter un groupe de ressources](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Gérer l’accès aux groupes de ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d’informations, consultez [gérer l’accès à l’aide de RBAC et Azure CLI](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’Azure Resource Manager, consultez [présentation d’Azure Resource Manager](./resource-group-overview.md).
- Pour découvrir la syntaxe du modèle Resource Manager, consultez [comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour savoir comment développer des modèles, consultez le [didacticiels pas à pas](/azure/azure-resource-manager/).
- Pour afficher les schémas de modèle Azure Resource Manager, consultez [référence de modèle](/azure/templates/).