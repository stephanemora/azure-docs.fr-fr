---
title: Créer des rôles personnalisés à l’aide d’Azure PowerShell | Microsoft Docs
description: Découvrez comment créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle (RBAC) avec Azure PowerShell. Cet article indique également comment répertorier, créer, mettre à jour et supprimer des rôles personnalisés.
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
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6020aa0a770075526d8d07c94b847b5933a26c2a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428118"
---
# <a name="create-custom-roles-using-azure-powershell"></a>Créer des rôles personnalisés à l’aide d’Azure PowerShell

Si les [rôles intégrés](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Cet article explique comment créer et gérer des rôles personnalisés avec Azure PowerShell.

## <a name="prerequisites"></a>Prérequis

Pour créer des rôles personnalisés, vous avez besoin des éléments suivants :

- Autorisations nécessaires pour créer des rôles personnalisés, tels que [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator)
- [Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps) installé localement

## <a name="list-custom-roles"></a>Répertorier les rôles personnalisés

Pour répertorier les rôles pouvant être attribués dans une étendue, utilisez la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition). L’exemple suivant répertorie tous les rôles pouvant être affectés dans l’abonnement sélectionné.

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

L’exemple suivant répertorie les rôles personnalisés pouvant être attribués dans l’abonnement sélectionné.

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Si l’abonnement sélectionné ne se trouve pas dans le `AssignableScopes` du rôle, le rôle personnalisé ne sera pas répertorié.

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Pour créer un rôle personnalisé, utilisez la commande [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition). Il existe deux méthodes pour structurer le rôle : avec un objet `PSRoleDefinition` ou un modèle JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Obtenir les opérations d’un fournisseur de ressources

Si vous créez des rôles personnalisés, il est important d’obtenir toutes les opérations possibles auprès des fournisseurs de ressources.
Vous pouvez afficher la liste des [opérations du fournisseur de ressources](resource-provider-operations.md) ou utiliser la commande [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) pour obtenir ces informations.
Par exemple, si vous souhaitez vérifier toutes les opérations disponibles pour des machines virtuelles, utilisez cette commande :

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Créer un rôle personnalisé avec l’objet PSRoleDefinition

Lorsque vous utilisez PowerShell pour créer un rôle personnalisé, vous pouvez utiliser l’un des [rôles intégrés](built-in-roles.md) comme point de départ ou en créer un intégralement. Le premier exemple de cette section commence par un rôle intégré, qui est ensuite personnalisé avec des privilèges supplémentaires. Modifiez les attributs et ajoutez les `Actions`, `NotActions` ou `AssignableScopes` de votre choix, puis enregistrez les modifications sous un nouveau rôle.

L’exemple suivant commence par le rôle intégré [Contributeur de machines virtuelles](built-in-roles.md#virtual-machine-contributor) et l’utilise pour créer un rôle personnalisé appelé *Opérateur de machines virtuelles*. Le nouveau rôle accorde l’accès à toutes les opérations des fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage* et *Microsoft.Network*, ainsi que l’accès pour démarrer, redémarrer et surveiller des machines virtuelles. Le rôle personnalisé peut être utilisé dans deux abonnements.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

L’exemple suivant montre une autre méthode pour créer le rôle personnalisé *Opérateur de machines virtuelles*. Il illustre d’abord la création d’un objet `PSRoleDefinition`. Les opérations d’action sont spécifiées dans la variable `perms` et sont définies sur la propriété `Actions`. La propriété `NotActions` est définie en lisant les `NotActions` dans le rôle intégré [Contributeur de machines virtuelles](built-in-roles.md#virtual-machine-contributor). Étant donné que le [Contributeur de machines virtuelles](built-in-roles.md#virtual-machine-contributor) ne contient pas de `NotActions`, cette ligne n’est pas nécessaire. Toutefois, elle montre comment les informations peuvent être récupérées à partir d’un autre rôle.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Créer un rôle personnalisé avec le modèle JSON

Un modèle JSON peut servir de définition source pour le rôle personnalisé. L’exemple suivant crée un rôle personnalisé qui autorise l’accès en lecture au stockage et aux ressources de calcul, ainsi que l’accès au support, et ajoute ce rôle à deux abonnements. Créez un fichier `C:\CustomRoles\customrole1.json` avec l’exemple de contenu suivant. L’ID doit être défini sur `null` lors de la création du rôle, étant donné qu’un nouvel ID est automatiquement généré. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Pour ajouter le rôle aux abonnements, exécutez la commande PowerShell suivante :

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Mettre à jour un rôle personnalisé

Comme pour la création d’un rôle personnalisé, vous pouvez modifier un rôle personnalisé existant à l’aide de l’objet `PSRoleDefinition` ou d’un modèle JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Mettre à jour un rôle personnalisé avec l’objet PSRoleDefinition

Pour modifier un rôle personnalisé, utilisez d’abord la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour récupérer la définition du rôle. Apportez ensuite les modifications souhaitées à la définition de rôle. Enfin, utilisez la commande [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) pour enregistrer la définition de rôle modifiée.

L’exemple suivant ajoute l’opération `Microsoft.Insights/diagnosticSettings/*` au rôle personnalisé *Opérateur de machine virtuelle* .

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

L’exemple suivant ajoute un abonnement Azure aux étendues attribuables du rôle personnalisé *Opérateur de machine virtuelle* .

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Mettre à jour un rôle personnalisé avec un modèle JSON

À l’aide du modèle JSON précédent, vous pouvez facilement modifier un rôle personnalisé existant pour ajouter ou supprimer des actions. Mettez à jour le modèle JSON et ajoutez l’action de lecture pour la mise en réseau, comme l’indique l’exemple suivant. Les définitions figurant dans le modèle ne sont pas cumulativement appliquées à une définition existante, ce qui veut dire que le rôle s’affiche exactement comme vous le spécifiez dans le modèle. Vous devez aussi mettre à jour le champ ID avec l’ID du rôle. Si vous n’êtes pas certain de cette valeur, vous pouvez utiliser l’applet de commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour obtenir ces informations.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Pour mettre à jour le rôle existant, exécutez la commande PowerShell suivante :

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

Pour supprimer un rôle personnalisé, utilisez la commande [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition).

L’exemple suivant supprime le rôle personnalisé *Opérateur de machine virtuelle* .

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Créer un rôle personnalisé à l'aide d'Azure PowerShell](tutorial-custom-role-powershell.md)
- [Rôles personnalisés dans Azure](custom-roles.md)
- [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md)
