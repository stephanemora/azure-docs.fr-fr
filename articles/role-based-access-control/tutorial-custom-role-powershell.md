---
title: 'Tutoriel : Créer un rôle personnalisé à l’aide d’Azure PowerShell | Microsoft Docs'
description: Découvrez comment créer un rôle personnalisé à l’aide d’Azure PowerShell.
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
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: 1e3043a6053625f8363e9035e59b53275714a827
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300607"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Tutoriel : Créer un rôle personnalisé à l’aide d’Azure PowerShell

Si les [rôles intégrés](built-in-roles.md) ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Pour ce tutoriel, vous allez créer un rôle personnalisé nommé Reader Support Tickets à l’aide d’Azure PowerShell. Le rôle personnalisé permet à l’utilisateur de consulter tous les éléments de l’abonnement et d’ouvrir des tickets de support.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un rôle personnalisé
> * Répertorier les rôles personnalisés
> * Mettre à jour un rôle personnalisé
> * Supprimer un rôle personnalisé

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

- autorisations nécessaires pour créer des rôles personnalisés, par exemple, [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) ;
- [Azure PowerShell](/powershell/azure/install-azurerm-ps) installé localement

## <a name="sign-in-to-azure-powershell"></a>Se connecter à Azure PowerShell

Connectez-vous à [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Créer un rôle personnalisé

Le moyen le plus simple de créer un rôle personnalisé consiste à commencer avec un rôle prédéfini, à le modifier, puis à créer un nouveau rôle.

1. Dans PowerShell, utilisez la commande [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) pour obtenir la liste des opérations pour le fournisseur de ressources Microsoft.Support. Il est utile de connaître les opérations qui sont disponibles pour créer vos autorisations. Vous pouvez également consulter la liste de toutes les opérations dans [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Utilisez la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour générer le rôle [Lecteur](built-in-roles.md#reader) au format JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Ouvrez le fichier **ReaderSupportRole.json** dans un éditeur.

    Voici la sortie JSON. Pour plus d’informations sur les différentes propriétés, consultez [Rôles personnalisés](custom-roles.md).

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Modifiez le fichier JSON pour ajouter l’opération `"Microsoft.Support/*"` à la propriété `Actions`. Veillez à inclure une virgule après l’opération de lecture. Cette action autorise l’utilisateur à créer des tickets de support.

1. Obtenez l’ID de votre abonnement à l’aide de la commande [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. Dans `AssignableScopes`, ajoutez votre ID d’abonnement au format suivant : `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Vous devez ajouter des ID d’abonnements explicites, sinon vous ne serez pas autorisé à importer le rôle dans votre abonnement.

1. Supprimez la ligne de propriété `Id` et affectez la valeur `true` à la propriété `IsCustom`.

1. Remplacez les valeurs de propriétés `Name` et `Description` par « Reader Support Tickets » et « View everything in the subscription and also open support tickets ».

    Votre fichier JSON doit ressembler à ceci :

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. Pour créer le rôle personnalisé, utilisez la commande [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) et spécifiez le fichier de définition de rôle JSON.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Le nouveau rôle personnalisé est maintenant disponible dans le portail Azure et peut être affecté à des utilisateurs, des groupes ou des principaux de service tout comme des rôles intégrés.

## <a name="list-custom-roles"></a>Répertorier les rôles personnalisés

- Pour répertorier tous vos rôles personnalisés, utilisez la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Vous pouvez également voir le rôle personnalisé dans le portail Azure.

    ![capture d’écran d’un rôle personnalisé importé dans le portail Azure](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Mettre à jour un rôle personnalisé

Pour mettre à jour le rôle personnalisé, vous pouvez mettre à jour le fichier JSON ou utiliser l’objet `PSRoleDefinition`.

1. Pour mettre à jour le fichier JSON, utilisez la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour générer le rôle personnalisé au format JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Ouvrez le fichier dans un éditeur.

1. Dans `Actions`, ajoutez l’opération pour créer et gérer les déploiements de groupes de ressources `"Microsoft.Resources/deployments/*"`.

    Votre fichier JSON mis à jour doit ressembler à ceci :

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. Pour mettre à jour le rôle personnalisé, utilisez la commande [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) et spécifiez le fichier JSON mis à jour.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Pour utiliser l’objet `PSRoleDefintion` afin de mettre à jour votre rôle personnalisé, utilisez d’abord la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour obtenir le rôle.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Appelez la méthode `Add` pour ajouter l’opération de lecture des paramètres de diagnostic.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Utilisez la commande [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) pour mettre à jour le rôle.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Supprimer un rôle personnalisé

1. Utilisez la commande [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) pour obtenir l’ID du rôle personnalisé.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Utilisez la commande [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) et spécifiez l’ID de rôle pour supprimer le rôle personnalisé.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Lorsque vous êtes invité à confirmer votre choix, saisissez **Y**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des rôles personnalisés à l’aide de PowerShell](custom-roles-powershell.md)
