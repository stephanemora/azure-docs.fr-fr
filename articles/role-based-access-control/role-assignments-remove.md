---
title: Supprimer les attributions de rôles Azure - Azure RBAC
description: Découvrez comment supprimer l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du portail Azure, d’Azure PowerShell, d’Azure CLI ou de l’API REST.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100561120"
---
# <a name="remove-azure-role-assignments"></a>Supprimer des attributions de rôles Azure

Le [contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../articles/role-based-access-control/overview.md) est le système d’autorisation que vous utilisez pour gérer l’accès aux ressources Azure. Supprimez une attribution de rôle pour supprimer l’accès à une ressource Azure. Cet article explique la procédure de suppression des attributions de rôles à l’aide du portail Azure, d’Azure PowerShell, d’Azure CLI et de l’API REST.

## <a name="prerequisites"></a>Prérequis

Pour ajouter des attributions de rôles, vous devez disposer :

- d’autorisations `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) ou [Propriétaire](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Portail Azure

1. Ouvrez **Contrôle d’accès (IAM)** dans une étendue, par exemple un groupe d’administration, un abonnement, un groupe de ressources ou une ressource pour lesquels vous souhaitez supprimer l’accès.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

1. Dans la liste des attributions de rôle, ajoutez une coche à côté du principal de sécurité comportant l’attribution de rôle que vous souhaitez supprimer.

   ![Attribution de rôle sélectionnée pour être supprimée](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Cliquez sur **Supprimer**.

   ![Supprimer le message d’attribution de rôle](./media/role-assignments-remove/remove-role-assignment.png)

1. Dans le message d’attribution de rôle qui s’affiche, cliquez sur **Oui**.

    Si vous voyez un message indiquant que les attributions de rôles héritées ne peuvent pas être supprimées, cela signifie que vous tentez de supprimer une attribution de rôle dans une étendue enfant. Vous devez ouvrir le contrôle d’accès (IAM) dans l’étendue où le rôle a été attribué, puis réessayer. Un moyen rapide d’ouvrir le contrôle d’accès (IAM) à l’étendue correcte consiste à examiner la colonne **Étendue**, puis à cliquer sur le lien à côté de **(Héritée)** .

   ![Message Supprimer l’attribution de rôle pour les attributions de rôles héritées](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

Dans Azure PowerShell, supprimez une attribution de rôle en utilisant [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

L’exemple suivant retire l’attribution de rôle [Collaborateur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à l’utilisateur *patlong\@contoso.com* dans le groupe de ressources *pharma-sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Retire le rôle [Lecteur](built-in-roles.md#reader) au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue d’un abonnement.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Retire le rôle [Lecteur de facturation](built-in-roles.md#billing-reader) à l’utilisateur *alain\@example.com* dans l’étendue du groupe d’administration.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Si vous obtenez le message d’erreur : « Les informations fournies ne dirigent pas à une attribution de rôle », veillez à vérifier que vous avez aussi spécifié les paramètres `-Scope` ou `-ResourceGroupName`. Pour plus d’informations, consultez [Résoudre les problèmes liés à RBAC Azure](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>Azure CLI

Dans Azure CLI, supprimez une attribution de rôle à l’aide de [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete).

L’exemple suivant retire l’attribution de rôle [Collaborateur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à l’utilisateur *patlong\@contoso.com* dans le groupe de ressources *pharma-sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Retire le rôle [Lecteur](built-in-roles.md#reader) au groupe *Ann Mack Team* associé à l’ID 22222222-2222-2222-2222-222222222222 dans l’étendue d’un abonnement.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Retire le rôle [Lecteur de facturation](built-in-roles.md#billing-reader) à l’utilisateur *alain\@example.com* dans l’étendue du groupe d’administration.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>API REST

Dans l’API REST, supprimez une attribution de rôle à l’aide de [Attributions de rôles - Supprimer](/rest/api/authorization/roleassignments/delete).

1. Obtenir l’identificateur de l’attribution de rôle (GUID). Cet identificateur est renvoyé lorsque vous créez l’attribution de rôle, ou vous pouvez l’obtenir en répertoriant les attributions de rôles.

1. Commencez par la requête suivante :

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. Dans l’URI, remplacez *{scope}* par l’étendue de suppression de l’attribution de rôle.

    > [!div class="mx-tableFixed"]
    > | Étendue | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Groupe d’administration |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Ressource |

1. Remplacez *{roleAssignmentId}* par l’identificateur GUID de l’attribution de rôle.

La requête suivante supprime l’attribution de rôle spécifiée au niveau de l’étendue de l’abonnement :

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Voici un exemple de sortie :

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>Modèle ARM

Il n’existe aucun moyen de supprimer une attribution de rôle à l’aide d’un modèle Azure Resource Manager (modèle ARM). Pour supprimer une attribution de rôle, vous devez utiliser d’autres outils, tels que le portail Azure, Azure PowerShell, Azure CLI ou l’API REST.

## <a name="next-steps"></a>Étapes suivantes

- [Répertorier les attributions de rôle Azure à l’aide du portail Azure](role-assignments-list-portal.md)
- [Répertorier les attributions de rôle Azure à l’aide d’Azure PowerShell](role-assignments-list-powershell.md)
- [Résoudre les problèmes liés à Azure RBAC](troubleshooting.md)
