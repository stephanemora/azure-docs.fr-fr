---
title: Guide pratique pour modifier, supprimer ou gérer vos groupes d’administration dans Azure
description: Découvrez comment tenir et mettre à jour votre hiérarchie de groupes d’administration.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: rithorn
ms.openlocfilehash: a3de0df8fde3b271b7ba9bb9aab01dbcd5c3bf08
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991213"
---
# <a name="manage-your-resources-with-management-groups"></a>Gérer vos ressources avec des groupes d’administration

Les groupes d’administration sont des conteneurs qui vous aident à gérer l’accès, la stratégie et la conformité dans plusieurs abonnements. Vous pouvez modifier, supprimer et gérer ces conteneurs pour pouvoir utiliser des hiérarchies avec [Azure Policy](../policy/overview.md) et les [contrôles d’accès en fonction du rôle Azure](../../role-based-access-control/overview.md). Pour plus d’informations sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](overview.md).

Pour apporter des modifications à un groupe d’administration, vous devez avoir un rôle de propriétaire ou contributeur sur le groupe d’administration. Pour connaître vos autorisations, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Modifier le nom d’un groupe d’administration

Vous pouvez modifier le nom du groupe d’administration en utilisant le portail, PowerShell ou Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Modifier le nom dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration à renommer.

1. Sélectionnez l’option **Renommer le groupe** en haut de la page.

   ![Renommer le groupe](./media/detail_action_small.png)

1. Lorsque le menu s’ouvre, entrez le nouveau nom à afficher.

   ![Renommer le groupe](./media/rename_context.png)

1. Sélectionnez **Enregistrer**.

### <a name="change-the-name-in-powershell"></a>Modifier le nom dans PowerShell

Pour mettre à jour le nom d’affichage, utilisez **Update-AzureRmManagementGroup**. Par exemple, pour remplacer le nom de groupe d’administration « Contoso IT » par « Groupe Contoso », exécutez la commande suivante :

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Modifier le nom dans Azure CLI

Pour Azure CLI, utilisez la commande update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Supprimer un groupe d’administration

Pour supprimer un groupe d’administration, les conditions suivantes doivent être remplies :

1. Le groupe d’administration ne contient pas de groupes d’administration enfants ni d’abonnements.

   - Pour déplacer un abonnement en dehors d’un groupe d’administration, consultez [Déplacer un abonnement vers un autre groupe d’administration](#Move-subscriptions-in-the-hierarchy).

   - Pour déplacer un groupe d’administration vers un autre groupe d’administration, consultez [Déplacer des groupes d’administration dans la hiérarchie](#Move-management-groups-in-the-hierarchy).

1. Vous avez des autorisations en écriture sur le rôle de propriétaire ou contributeur du groupe d’administration. Pour connaître vos autorisations, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Supprimer dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration à supprimer.

1. Sélectionnez **Supprimer**.

   - Si l’icône est désactivée, placez le curseur de la souris au-dessus d’elle pour en connaître la raison.

   ![Supprimer un groupe](./media/delete.png)

1. Une fenêtre s’ouvre pour que vous confirmiez la suppression du groupe d’administration.

   ![Supprimer un groupe](./media/delete_confirm.png)

1. Sélectionnez **Oui**.

### <a name="delete-in-powershell"></a>Supprimer dans PowerShell

Utilisez la commande **Remove-AzureRmManagementGroup** dans PowerShell pour supprimer des groupes d’administration.

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Supprimer dans Azure CLI

Avec Azure CLI, utilisez la commande az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Afficher des groupes d’administration

Vous pouvez afficher tous les groupes pour lesquels vous avez un rôle RBAC direct ou hérité.  

### <a name="view-in-the-portal"></a>Afficher dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. La page de la hiérarchie du groupe d’administration apparaît, dans laquelle vous pouvez explorer tous les groupes d’administration et abonnements auxquels vous avez accès. Sélectionner le nom du groupe vous fait descendre d’un niveau dans la hiérarchie. La navigation fonctionne comme dans un explorateur de fichiers.

   ![Principal](./media/main.png)

1. Pour afficher les détails du groupe d’administration, sélectionnez le lien **(détails)** en regard du titre du groupe d’administration. Si ce lien n’est pas disponible, vous n’avez pas les autorisations pour afficher ce groupe d’administration.  

### <a name="view-in-powershell"></a>Afficher dans PowerShell

Utilisez la commande Get-AzureRmManagementGroup pour récupérer tous les groupes.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```

Pour consulter les détails d’un seul groupe d’administration, utilisez le paramètre -GroupName.

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName 'Contoso'
```

### <a name="view-in-azure-cli"></a>Afficher dans Azure CLI

Utilisez la commande list pour récupérer tous les groupes.  

```azurecli-interactive
az account management-group list
```

Pour consulter les détails d’un seul groupe d’administration, utilisez la commande show.

```azurecli-interactive
az account management-group show --name 'Contoso'
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Déplacer des abonnements dans la hiérarchie

L’une des raisons de créer un groupe d’administration est de regrouper des abonnements. Seuls les groupes d’administration et les abonnements peuvent être enfants d’un autre groupe d’administration. Un abonnement déplacé vers un groupe d’administration hérite de toutes les stratégies et de tous les accès utilisateur du groupe d’administration parent.

Pour déplacer l’abonnement, vous devez avoir deux autorisations :

- Rôle de « propriétaire » sur l’abonnement enfant.
- Rôle de « propriétaire » ou « contributeur » sur le nouveau groupe d’administration parent.
- Rôle de « propriétaire » ou « contributeur » sur l’ancien groupe d’administration parent.

Pour connaître vos autorisations, sélectionnez le groupe d’administration, puis sélectionnez **IAM**. Pour en savoir plus sur les rôles RBAC, consultez [Gérer l’accès et les autorisations avec le contrôle d’accès en fonction du rôle (RBAC)](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Déplacer des abonnements dans le portail

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Ajouter un abonnement existant à un groupe d’administration

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration que vous envisagez d’utiliser comme parent.

1. En haut de la page, sélectionnez **Ajouter un abonnement**.

1. Sélectionnez l’abonnement dans la liste portant le bon ID.

   ![Enfants](./media/add_context_sub.png)

1. Sélectionnez « Enregistrer ».

#### <a name="remove-a-subscription-from-a-management-group"></a>Supprimer un abonnement d’un groupe d’administration

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration qui est le parent actuel.  

1. Dans la liste, sélectionnez les points de suspension situés en fin de la ligne de l’abonnement à déplacer.

   ![Déplacer](./media/move_small.png)

1. Sélectionnez **Déplacer**.

1. Dans le menu qui s’ouvre, sélectionnez le **groupe d’administration parent**.

   ![Déplacer](./media/move_small_context.png)

1. Sélectionnez **Enregistrer**.

### <a name="move-subscriptions-in-powershell"></a>Déplacer des abonnements dans PowerShell

Pour déplacer un abonnement dans PowerShell, utilisez la commande Add-AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Pour supprimer le lien entre un abonnement et un groupe d’administration, utilisez la commande Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Déplacer des abonnements dans Azure CLI

Pour déplacer un abonnement dans CLI, utilisez la commande add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Pour supprimer l’abonnement du groupe d’administration, utilisez la commande subscription remove.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Déplacer des groupes d’administration dans la hiérarchie  

Lorsque vous déplacez un groupe d’administration parent, toutes les ressources enfants qui incluent des groupes d’administration, des abonnements, des groupes de ressources et des ressources se déplacent avec le parent.

### <a name="move-management-groups-in-the-portal"></a>Déplacer des groupes d’administration dans le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Tous les services** > **Groupes d’administration**.

1. Sélectionnez le groupe d’administration que vous envisagez d’utiliser comme parent.

1. En haut de la page, sélectionnez **Ajouter un groupe d’administration**.

1. Dans le menu qui s’ouvre, indiquez si vous souhaitez créer un groupe d’administration ou en utiliser un existant.

   - Si vous sélectionnez Nouveau, vous créez un groupe d’administration.
   - Si vous sélectionnez un groupe existant, une liste déroulante répertoriant tous les groupes d’administration s’affiche. Vous pouvez les déplacer vers ce groupe d’administration.  

   ![déplacer](./media/add_context_MG.png)

1. Sélectionnez **Enregistrer**.

### <a name="move-management-groups-in-powershell"></a>Déplacer des groupes d’administration dans PowerShell

Utilisez la commande Update-AzureRmManagementGroup dans PowerShell pour déplacer un groupe d’administration sous un autre groupe.

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'Contoso' -ParentName 'ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Déplacer des groupes d’administration dans Azure CLI

Utilisez la commande update pour déplacer un groupe d’administration avec Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent 'Contoso Tenant'
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les groupes d’administration, consultez :

- [Organiser vos ressources avec des groupes d’administration Azure](overview.md)
- [Créer des groupes d’administration pour organiser les ressources Azure](create.md)
- [Installer le module Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Passer en revue les spécifications de l’API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installer l’extension Azure CLI](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)