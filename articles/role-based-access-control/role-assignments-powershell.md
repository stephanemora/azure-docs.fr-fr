---
title: Ajouter ou supprimer des attributions de rôle avec le contrôle d’accès en fonction du rôle et Azure PowerShell
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide du contrôle d’accès en fonction du rôle (RBAC) Azure et d’Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79236925"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Ajouter ou supprimer des attributions de rôles à l’aide du RBAC Azure et d’Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide d’Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Pour ajouter ou supprimer des attributions de rôles, vous devez disposer :

- d’autorisations `Microsoft.Authorization/roleAssignments/write` et `Microsoft.Authorization/roleAssignments/delete`, telles que [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire de l’accès utilisateur](built-in-roles.md#owner)
- [PowerShell dans Azure Cloud Shell](/azure/cloud-shell/overview) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Récupérer des ID d’objet

Pour ajouter ou supprimer des attributions de rôles, vous devrez peut-être spécifier l’ID unique d’un objet. L’ID a le format : `11111111-1111-1111-1111-111111111111`. Vous pouvez récupérer l’ID à l’aide du Portail Azure ou d’Azure PowerShell.

### <a name="user"></a>Utilisateur

Pour récupérer l’ID d’objet pour un utilisateur Azure AD, vous pouvez utiliser [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Groupe

Pour récupérer l’ID d’objet pour un groupe Azure AD, vous pouvez utiliser [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Application

Pour récupérer l’ID objet d’un principal de service Azure AD (identité utilisée par une application), vous pouvez utiliser [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Pour un principal de service, utilisez l’ID d’objet et **non** l’ID d’application.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Ajouter une attribution de rôle

Dans le contrôle RBAC, vous ajoutez une attribution de rôle pour accorder l’accès.

### <a name="user-at-a-resource-group-scope"></a>Utilisateur dans l’étendue d’un groupe de ressources

Pour ajouter une attribution de rôle pour un utilisateur dans l’étendue d’un groupe de ressources, utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Utilisation de l’ID de rôle unique

Un nom de rôle peut changer dans certaines circonstances, par exemple :

- Vous utilisez vos propres rôles personnalisés et vous décidez d’en modifier le nom.
- Vous utilisez un rôle en préversion dont le nom contient **(préversion)** . Lorsque le rôle est publié, le rôle est renommé.

> [!IMPORTANT]
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Même si un rôle est renommé, l’ID de rôle ne change pas. Si vous utilisez des scripts ou une automatisation pour créer vos attributions de rôles, il est recommandé d’utiliser l’ID de rôle unique au lieu du nom de rôle. Par conséquent, si un rôle est renommé, vos scripts sont plus susceptibles de fonctionner.

Pour ajouter une attribution de rôle à l’aide de l’ID de rôle unique au lieu du nom de rôle, utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

L’exemple suivant attribue le rôle [Contributeur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à l’utilisateur *alain\@exemple.com* dans l’étendue du groupe de ressources *pharma-sales*. Pour obtenir l’ID unique de rôle, vous pouvez utiliser [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) ou consulter la liste des [rôles intégrés pour les ressources Azure](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Groupe dans l’étendue d’une ressource

Pour ajouter une attribution de rôle pour un groupe dans l’étendue d’une ressource, utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Pour plus d’informations sur la récupération de l’ID d’objet du groupe, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Application dans l’étendue d’un abonnement

Pour ajouter une attribution de rôle pour une application dans l’étendue d’un abonnement, utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Pour plus d’informations sur la récupération de l’ID d’objet de l’application, consultez [Récupérer des ID d’objet](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Utilisateur dans l’étendue d’un groupe d’administration

Pour ajouter une attribution de rôle pour un utilisateur dans l’étendue d’un groupe d’administration, utilisez [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Pour obtenir l’ID du groupe d’administration, accédez au panneau **Groupes d’administration** dans le portail Azure ou utilisez [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Supprimer une attribution de rôle

Pour supprimer l’accès dans RBAC, supprimez une attribution de rôle en utilisant [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

L’exemple suivant retire l’attribution de rôle *Collaborateur de machine virtuelle* à l’utilisateur *alain\@example.com* dans le groupe de ressources *pharma-sales* :

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

L’exemple suivant retire le rôle <nom_du_rôle> à <id_objet> dans l’étendue d’un abonnement.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

L’exemple suivant retire le rôle <nom_du_rôle> à <id_objet> dans l’étendue d’un groupe d’administration.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Si vous obtenez le message d’erreur : « Les informations fournies ne dirigent pas à une attribution de rôle », veillez à vérifier que vous avez aussi spécifié les paramètres `-Scope` ou `-ResourceGroupName`. Pour plus d’informations, consultez [Résoudre des problèmes liés au contrôle d’accès en fonction du rôle pour les ressources Azure](troubleshooting.md#role-assignments-with-unknown-security-principal).

## <a name="next-steps"></a>Étapes suivantes

- [Lister les attributions de rôles à l’aide du RBAC Azure et d’Azure PowerShell](role-assignments-list-powershell.md)
- [Tutoriel : Permettre à un groupe d'accéder aux ressources Azure à l'aide du contrôle RBAC et d'Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l'aide d'Azure PowerShell](tutorial-custom-role-powershell.md)
- [Gérer les ressources avec Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
