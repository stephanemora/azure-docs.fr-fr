---
title: Tutoriel - Accorder l’accès à un groupe avec RBAC et Azure PowerShell | Microsoft Docs
description: Utilisez le contrôle d’accès en fonction du rôle (RBAC) pour accorder l’accès à un groupe pour afficher tous les éléments d’un abonnement et gérer tous les éléments d’un groupe de ressources à l’aide d’Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 8bb06493683dabb92dfe75f371f96db14a7951b3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301001"
---
# <a name="tutorial-grant-access-for-a-group-using-rbac-and-azure-powershell"></a>Tutoriel : Accorder l’accès à un groupe avec RBAC et Azure PowerShell

Le [Contrôle d’accès en fonction du rôle (RBAC)](overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Dans ce tutoriel, vous accordez l’accès à un groupe pour afficher tous les éléments d’un abonnement et gérer tous les éléments d’un groupe de ressources à l’aide d’Azure PowerShell.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Accorder l’accès à un groupe dans différentes étendues
> * Répertorier les accès
> * Suppression d'accès

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- Autorisations pour créer des groupes dans Azure Active Directory (ou disposer d’un groupe existant)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Affectations de rôles

Dans RBAC, pour accorder l’accès, vous créez une attribution de rôle. Une attribution de rôle se compose de trois éléments : un principal de sécurité, une définition de rôle et une étendue. Voici deux attributions de rôles que vous allez effectuer dans ce tutoriel :

| Principal de sécurité | Définition de rôle | Étendue |
| --- | --- | --- |
| Groupe<br>(Groupe du tutoriel RBAC) | [Lecteur](built-in-roles.md#reader) | Abonnement |
| Groupe<br>(Groupe du tutoriel RBAC)| [Contributeur](built-in-roles.md#contributor) | Groupe de ressources<br>(rbac-tutorial-resource-group) |

   ![Attributions de rôles pour un groupe](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Créer un groupe

Pour attribuer un rôle, vous avez besoin d’un utilisateur, d’un groupe ou d’un principal du service. Si vous n’avez pas déjà un groupe, vous pouvez en créer un.

- Dans Azure Cloud Shell, créez un nouveau groupe à l’aide de la commande [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Si vous n’êtes pas autorisé à créer des groupes, vous pouvez essayer le [Tutoriel : accorder l’accès à un utilisateur avec RBAC et Azure PowerShell](tutorial-role-assignments-user-powershell.md) à la place.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Vous utilisez un groupe de ressources pour montrer comment attribuer un rôle à une étendue de groupe de ressources.

1. Obtenez la liste des emplacements de région à l’aide de la commande [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. Sélectionnez un emplacement près de chez vous et assignez-le à une variable.

   ```azurepowershell
   $location = "westus"
   ```

1. Créez un nouveau groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Accorder l'accès

Pour accorder l’accès au groupe, vous utilisez la commande [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) afin d’attribuer un rôle. Vous devez spécifier le principal de sécurité, la définition de rôle et l’étendue.

1. Obtenez l’ID d’objet du groupe en utilisant la commande [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Enregistrez l’ID d’objet du groupe dans une variable.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Obtenez l’ID de votre abonnement à l’aide de la commande [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Enregistrez l’étendue de l’abonnement dans une variable.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Attribuez le rôle de [Lecteur](built-in-roles.md#reader) au groupe dans l’étendue de l’abonnement.

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Attribuez le rôle de [Contributeur](built-in-roles.md#contributor) au groupe dans l’étendue du groupe de ressources.

    ```azurepowershell
    New-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Répertorier les accès

1. Pour vérifier l’accès à l’abonnement, utilisez la commande [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) pour répertorier les attributions de rôle.

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Dans la sortie, vous pouvez voir que le rôle de Lecteur a été attribué au groupe du tutoriel RBAC dans l’étendue de l’abonnement.

1. Pour vérifier l’accès au groupe de ressources, utilisez la commande [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) pour répertorier les attributions de rôle.

    ```azurepowershell
    Get-AzureRmRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Dans la sortie, vous pouvez voir que les rôles de Collaborateur et de Lecteur ont été attribués au groupe du tutoriel RBAC. Le rôle de Contributeur est dans l’étendue rbac-tutorial-resource-group et le rôle de Lecteur est hérité dans l’étendue de l’abonnement.

## <a name="optional-list-access-using-the-azure-portal"></a>(Facultatif) Accès à la liste à l’aide du portail Azure

1. Pour voir les attributions de rôle dans le portail Azure, affichez le panneau **Contrôle d’accès (IAM)** pour l’abonnement.

    ![Attributions de rôles pour un groupe dans l’étendue de l’abonnement](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Affichez le panneau **Contrôle d’accès (IAM)** pour le groupe de ressources.

    ![Attributions de rôles pour un groupe dans l’étendue du groupe de ressources](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Suppression d'accès

Pour supprimer l’accès pour des utilisateurs, des groupes et des applications, utilisez [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) afin de supprimer une attribution de rôle.

1. Utilisez la commande suivante pour supprimer l’attribution de rôle de Collaborateur pour le groupe dans l’étendue de groupe de ressources.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Utilisez la commande suivante pour supprimer l’attribution de rôle de Lecteur pour le groupe dans l’étendue d’abonnement.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Supprimer les ressources

Pour supprimer les ressources créées par ce tutoriel, supprimez le groupe de ressources et le groupe.

1. Supprimez le groupe de ressources à l’aide de la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Lorsque vous êtes invité à confirmer votre choix, saisissez **Y**. La suppression prendra quelques secondes.

1. Supprimez le groupe à l’aide de la commande [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Si vous recevez une erreur lorsque vous essayez de supprimer le groupe, vous pouvez également supprimer le groupe dans le portail.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer l’accès avec RBAC et PowerShell](role-assignments-powershell.md)
