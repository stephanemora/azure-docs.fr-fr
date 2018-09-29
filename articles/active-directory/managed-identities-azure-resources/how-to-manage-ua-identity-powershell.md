---
title: Guide pratique pour créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell
description: Instructions détaillées pour créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: c7191f60b8780e8ccee9b330aa21d8174f0f0148
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106376"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Créer, répertorier ou supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Les identités managées pour ressources Azure fournissent aux services Azure une identité managée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Installez [la dernière version d’Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si ce n’est déjà fait.
- Si vous choisissez d’installer et d’utiliser PowerShell en local, ce didacticiel nécessite le module Azure PowerShell version 5.7.0 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Si vous exécutez PowerShell en local, vous devez également effectuer les opérations suivantes : 
    - Exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.
    - Installez la [dernière version de PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Exécutez `Install-Module -Name PowerShellGet -AllowPrerelease` pour obtenir la préversion du module `PowerShellGet` (vous devrez peut-être utiliser la commande `Exit` pour quitter la session PowerShell actuelle après avoir exécuté cette commande pour installer le module `AzureRM.ManagedServiceIdentity`).
    - Exécutez `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` pour installer la version préliminaire du module `AzureRM.ManagedServiceIdentity` afin d’effectuer les opérations d’identité managée affectée par l’utilisateur décrites dans cet article.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer des attributions des rôles suivants :
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pour créer, lire (répertorier), mettre à jour et supprimer une identité managée affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour lire (répertorier) les propriétés d’une identité managée affectée par l’utilisateur.

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée affectée par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, utilisez la commande [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity). Le paramètre `ResourceGroupName` spécifie le groupe de ressources dans lequel créer l’identité managée affectée par l’utilisateur, et le paramètre `-Name` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour répertorier les identités affectées par l’utilisateur, utilisez la commande [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity).  Le paramètre `-ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée. Remplacez `<RESOURCE GROUP>` par votre propre valeur :

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Dans la réponse, les identités managées affectées par l’utilisateur ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, utilisez la commande [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity).  Le paramètre `-ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée, et le paramètre `-Name` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> La suppression d’une identité managée affectée par l’utilisateur n’a pas pour effet de supprimer la référence d’une ressource à laquelle elle a été affectée. Les attributions d’identités doivent être supprimées séparément.

## <a name="next-steps"></a>Étapes suivantes

Pour la liste complète et les détails des commandes d’identités managées pour ressources Azure, voir [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).
