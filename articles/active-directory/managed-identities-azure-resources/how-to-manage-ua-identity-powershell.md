---
title: Créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell – Azure AD
description: Instructions détaillées pour créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c512a867685b4480c7b31ac582e2cee069ee2447
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547399"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Créer, répertorier ou supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Les identités managées pour ressources Azure fournissent aux services Azure une identité managée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, répertorier et supprimer une identité managée affectée par l’utilisateur en utilisant Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, voir la [section Vue d’ensemble](overview.md). **Veillez à lire [la différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Installez [la dernière version d’Azure PowerShell](/powershell/azure/install-az-ps) si ce n’est déjà fait.
- Si vous exécutez PowerShell en local, vous devez également effectuer les opérations suivantes : 
    - Exécutez `Connect-AzAccount` pour créer une connexion avec Azure.
    - Installez la [dernière version de PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Exécutez `Install-Module -Name PowerShellGet -AllowPrerelease` pour obtenir la préversion du module `PowerShellGet` (vous devrez peut-être utiliser la commande `Exit` pour quitter la session PowerShell actuelle après avoir exécuté cette commande pour installer le module `Az.ManagedServiceIdentity`).
    - Exécutez `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` pour installer la version préliminaire du module `Az.ManagedServiceIdentity` afin d’effectuer les opérations d’identité managée affectée par l’utilisateur décrites dans cet article.

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Pour créer une identité managée affectée par l’utilisateur, utilisez la commande `New-AzUserAssignedIdentity`. Le paramètre `ResourceGroupName` spécifie le groupe de ressources dans lequel créer l’identité managée affectée par l’utilisateur, et le paramètre `-Name` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Répertorier les identités managées affectées par l’utilisateur

Pour créer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Pour répertorier les identités affectées par l’utilisateur, utilisez la commande [Get-AzUserAssigned].  Le paramètre `-ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée. Remplacez `<RESOURCE GROUP>` par votre propre valeur :

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Dans la réponse, les identités managées affectées par l’utilisateur ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Supprimer une identité managée affectée par l’utilisateur

Pour supprimer une identité managée affectée par l’utilisateur, votre compte a besoin de l’affectation de rôle [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor).

Pour supprimer une identité managée affectée par l’utilisateur, utilisez la commande `Remove-AzUserAssignedIdentity`.  Le paramètre `-ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité managée affectée par l’utilisateur a été créée, et le paramètre `-Name` spécifie son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> La suppression d’une identité managée affectée par l’utilisateur n’a pas pour effet de supprimer la référence d’une ressource à laquelle elle a été affectée. Les attributions d’identités doivent être supprimées séparément.

## <a name="next-steps"></a>Étapes suivantes

Pour la liste complète et les détails des commandes d’identités managées Azure PowerShell pour ressources Azure, consultez [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
