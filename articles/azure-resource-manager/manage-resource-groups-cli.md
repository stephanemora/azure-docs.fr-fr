---
title: Gérer des groupes de ressources - Azure CLI
description: Utilisez Azure CLI pour gérer vos groupes de ressources dans Azure Resource Manager. Montre comment créer, lister et supprimer des groupes de ressources.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 68b82425fe5eb7bc9dce633b21a251de3fe463c9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149812"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Gérer des groupes de ressources Azure Resource Manager à l’aide d’Azure CLI

Découvrez comment utiliser Azure CLI avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos groupes de ressources Azure. Pour gérer des ressources Azure, voir [Gérer les ressources Azure à l’aide d’Azure CLI](./manage-resources-cli.md).

Autres articles sur la gestion des groupes de ressources :

- [Gérer les groupes de ressources Azure à l’aide du portail Azure](./manage-resources-portal.md)
- [Gérer les groupes de ressources Azure à l’aide d’Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Qu’est-ce qu’un groupe de ressources

Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. En règle générale, il convient d’ajouter des ressources qui partagent le même cycle de vie dans un même groupe de ressources afin de pouvoir facilement les déployer, les mettre à jour et les supprimer en tant que groupe.

Le groupe de ressources stocke des métadonnées sur les ressources. Par conséquent, lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

Le groupe de ressources stocke des métadonnées sur les ressources. Lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées.

## <a name="create-resource-groups"></a>Créer des groupes de ressources

Le script CLI suivant crée un groupe de ressources, puis l’affiche.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Répertorier les groupes de ressources

Le script CLI suivant répertorie les groupes de ressources de votre abonnement.

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

Pour plus d’informations sur l’ordre dans lequel Azure Resource Manager supprime des ressources, voir [Suppression d’un groupe de ressources par Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Déployer des ressources sur un groupe de ressources existant

Voir [Déployer des ressources sur un groupe de ressources existant](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Déployer un groupe de ressources et des ressources

Vous pouvez créer un groupe de ressources et déployer des ressources sur ce groupe à l'aide d'un modèle Resource Manager. Pour plus d'informations, consultez [Créer un groupe de ressources et déployer des ressources](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Redéploiement en cas d’échec du déploiement

Cette fonctionnalité est également appelée *Annulation en cas d'erreur*. Pour plus d'informations, consultez [Redéploiement en cas d'échec du déploiement](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Déplacer vers un autre groupe de ressources ou abonnement

Vous pouvez déplacer les ressources du groupe vers un autre groupe de ressources. Pour plus d’informations, voir [Déplacer des ressources](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Verrouiller des groupes de ressources

Le verrouillage empêche d'autres utilisateurs de votre organisation de supprimer ou de modifier accidentellement des ressources stratégiques, telles qu'une ressource, un groupe de ressources ou un abonnement Azure. 

Le script suivant verrouille un groupe de ressources pour empêcher sa suppression.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Le script suivant obtient tous les verrous relatifs à un groupe de ressources :

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

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d'informations, consultez [Organisation des ressources Azure à l'aide d'étiquettes](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exporter des groupes de ressources dans des modèles

Après avoir correctement configuré votre groupe de ressources, vous pouvez afficher le modèle Resource Manager correspondant. L’exportation du modèle offre deux avantages :

- Automatisez les futurs déploiements de la solution, car le modèle contient la totalité de l'infrastructure.
- Familiarisez-vous avec la syntaxe des modèles en consultant la notation JSON (JavaScript Object Notation) qui représente votre solution.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Le script affiche le modèle sur la console.  Copiez le JSON et enregistrez-le dans un fichier.

Pour plus d’informations, consultez [Export d’une ressource unique ou de plusieurs ressources vers un modèle dans le portail Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gérer l'accès aux groupes de ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d’informations, consultez [Gérer l’accès à l’aide du contrôle RBAC et d’Azure CLI](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour vous familiariser avec Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](./resource-group-overview.md).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour apprendre à développer des modèles, consultez les [tutoriels pas à pas](/azure/azure-resource-manager/).
- Pour accéder aux schémas liés aux modèles Azure Resource Manager, consultez [Informations de référence sur les modèles](/azure/templates/).