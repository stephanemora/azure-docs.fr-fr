---
title: Attribuer des rôles Azure à l’aide d’Azure PowerShell - RBAC Azure
description: Découvrez comment accorder l’accès aux ressources Azure pour les utilisateurs, groupes, principaux de service ou identités managées à l’aide d’Azure PowerShell et du contrôle d’accès en fonction du rôle (RBAC) Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 00f663b90f34f3b557329692f844bbbc1bf3207d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556803"
---
# <a name="assign-azure-roles-using-azure-powershell"></a>Attribuer des rôles Azure à l’aide d’Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Cet article explique comment attribuer des rôles à l’aide d’Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Pour attribuer des rôles, vous devez disposer des éléments suivants :

- Autorisations `Microsoft.Authorization/roleAssignments/write`, telles que [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ou [Propriétaire de l’accès utilisateur](built-in-roles.md#owner)
- [PowerShell dans Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure PowerShell](/powershell/azure/install-az-ps)
- Le compte que vous utilisez pour exécuter la commande PowerShell doit disposer de l’autorisation Microsoft Graph `Directory.Read.All`.

## <a name="steps-to-assign-an-azure-role"></a>Procédure d’attribution d’un rôle Azure

L’attribution d’un rôle se compose de trois éléments : un principal de sécurité, une définition de rôle et une étendue.

### <a name="step-1-determine-who-needs-access"></a>Étape 1 : Déterminer qui a besoin d’un accès

Vous pouvez attribuer un rôle à un utilisateur, à un groupe, à un principal de service ou à une identité managée. Pour attribuer un rôle, vous devrez peut-être spécifier l’ID unique de l’objet. L’ID a le format : `11111111-1111-1111-1111-111111111111`. Vous pouvez récupérer l’ID à l’aide du Portail Azure ou d’Azure PowerShell.

**Utilisateur**

Pour un utilisateur Azure AD, récupérez le nom d’utilisateur principal, par exemple *patlong\@contoso.com* ou l’ID d’objet utilisateur. Pour récupérer l’ID de l’objet, vous pouvez utiliser [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Groupe**

Pour un groupe Azure AD, vous avez besoin de l’ID d’objet de groupe. Pour récupérer l’ID de l’objet, vous pouvez utiliser [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Principal du service**

Pour un principal de service Azure AD (identité utilisée par une application), vous avez besoin de l’ID d’objet du principal de service. Pour récupérer l’ID de l’objet, vous pouvez utiliser [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Pour un principal de service, utilisez l’ID d’objet et **non** l’ID d’application.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Identité gérée**

Pour une identité managée affectée par le système ou par l’utilisateur, vous avez besoin de l’ID d’objet. Pour récupérer l’ID de l’objet, vous pouvez utiliser [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-select-the-appropriate-role"></a>Étape 2 : Sélectionner le rôle approprié

Les autorisations sont regroupées dans des rôles. Vous pouvez choisir parmi une liste de plusieurs [rôles intégrés Azure](built-in-roles.md) ou utiliser vos propres rôles personnalisés. Accorder l’accès avec le moindre privilège requis étant recommandé, vous devez éviter d’attribuer un rôle plus large.

Pour lister les rôles et récupérer l’ID de rôle unique, vous pouvez utiliser [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Voici comment répertorier les détails d’un rôle particulier.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Pour plus d’informations, consultez [Répertorier les définitions de rôle Azure](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Étape 3 : Identifier l’étendue nécessaire

Azure fournit quatre niveaux d’étendue : ressource, [groupe de ressources](../azure-resource-manager/management/overview.md#resource-groups), abonnement et [groupe d’administration](../governance/management-groups/overview.md). Accorder l’accès avec le moindre privilège requis étant recommandé, vous devez éviter d’attribuer un rôle à une étendue plus large. Pour plus d’informations sur l’étendue, consultez [Comprendre l’étendue](scope-overview.md).

**Étendue des ressources**

Pour l’étendue des ressources, vous avez besoin de l’ID de la ressource. Vous pouvez trouver l’ID de ressource en examinant les propriétés de la ressource dans le portail Azure. Un ID de ressource a le format suivant.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Étendue du groupe de ressources**

Pour l’étendue du groupe de ressources, vous avez besoin du nom du groupe de ressources. Vous trouverez le nom dans la page **Groupes de ressources** du portail Azure, ou vous pouvez utiliser [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Étendue d’abonnement** 

Pour l’étendue d’abonnement, vous avez besoin de l’ID d’abonnement. Vous pouvez trouver l’ID dans la page **Abonnements** du portail Azure, ou vous pouvez utiliser [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Étendue d’un groupe d’administration** 

Pour l’étendue d’un groupe d’administration, vous avez besoin du nom du groupe d’administration. Vous pouvez trouver le nom dans la page **Groupes d’administration** du portail Azure, ou vous pouvez utiliser [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-assign-role"></a>Étape 4 : Affecter le rôle

Pour attribuer un rôle, utilisez la commande [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). En fonction de l’étendue, la commande a généralement l’un des formats suivants.

**Étendue des ressources**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Étendue du groupe de ressources**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Étendue d’abonnement** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Étendue d’un groupe d’administration** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="assign-role-examples"></a>Attribuer des exemples de rôles

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Attribuer un rôle pour tous les conteneurs de blobs dans l’étendue d’une ressource de compte de stockage

Attribue le rôle [Contributeur aux données Blob du stockage](built-in-roles.md#storage-blob-data-contributor) à un principal de service associé à l’ID d’objet *55555555-5555-5555-5555-555555555555* dans l’étendue des ressources d’un compte de stockage nommé *storage12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Attribuer un rôle pour l’étendue d’une ressource d’un conteneur de blobs spécifique

Attribue le rôle [Contributeur aux données Blob du stockage](built-in-roles.md#storage-blob-data-contributor) à un principal de service associé à l’ID d’objet *55555555-5555-5555-5555-555555555555* dans l’étendue des ressources d’un conteneur de blobs nommé *blob-container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Attribuer un rôle pour un groupe dans l’étendue d’une ressource de réseau virtuel spécifique

Attribue le rôle [Contributeur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) au groupe *Pharma Sales Admins* associé à l’ID aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa dans l’étendue des ressources d’un réseau virtuel nommé *pharma-sales-project-network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Attribuer un rôle à un utilisateur dans l’étendue d’un groupe de ressources

Attribue le rôle [Contributeur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à l’utilisateur *patlong\@contoso.com* dans l’étendue du groupe de ressources *pharma-sales*.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Vous pouvez également spécifier le groupe de ressources complet avec le paramètre `-Scope` :

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Attribuer un rôle pour un utilisateur utilisant l’ID de rôle unique dans l’étendue d’un groupe de ressources

Un nom de rôle peut changer dans certaines circonstances, par exemple :

- Vous utilisez vos propres rôles personnalisés et vous décidez d’en modifier le nom.
- Vous utilisez un rôle en préversion dont le nom contient **(préversion)** . Lorsque le rôle est publié, le rôle est renommé.

Même si un rôle est renommé, l’ID de rôle ne change pas. Si vous utilisez des scripts ou une automatisation pour créer vos attributions de rôles, il est recommandé d’utiliser l’ID de rôle unique au lieu du nom de rôle. Par conséquent, si un rôle est renommé, vos scripts sont plus susceptibles de fonctionner.

L’exemple suivant attribue le rôle [Contributeur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à l’utilisateur *patlong\@contoso.com* dans l’étendue du groupe de ressources *pharma-sales*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Attribuer un rôle pour une application dans l’étendue d’un groupe de ressources

Attribue le rôle [Contributeur de machine virtuelle](built-in-roles.md#virtual-machine-contributor) à une application associée à l’ID d’objet de principal de service 77777777-7777-7777-7777-777777777777 dans l’étendue du groupe de ressources *pharma-sales*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Attribuer un rôle pour un utilisateur dans l’étendue d’un abonnement

Attribue le rôle [Lecteur](built-in-roles.md#reader) à l’utilisateur *annm\@example.com* dans l’étendue d’un abonnement.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Attribuer un rôle pour un utilisateur dans l’étendue d’un groupe d’administration

Attribue le rôle [Lecteur de facturation](built-in-roles.md#billing-reader) à l’utilisateur *alain\@example.com* dans l’étendue d’un groupe d’administration.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

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

## <a name="next-steps"></a>Étapes suivantes

- [Répertorier les attributions de rôle Azure à l’aide d’Azure PowerShell](role-assignments-list-powershell.md)
- [Tutoriel : Accorder à un groupe l’accès aux ressources Azure à l’aide d’Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Gérer les ressources avec Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
