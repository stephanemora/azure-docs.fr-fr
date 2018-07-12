---
title: Guide pratique pour créer, lister et supprimer une identité MSI attribuée à l’utilisateur à l’aide d’Azure PowerShell
description: Instructions étape par étape pour créer, lister et supprimer une identité MSI (Managed Service Identity) attribuée à l’utilisateur avec Azure PowerShell.
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
ms.openlocfilehash: ca0493d43abb5d1e79ffb28e45b427eef0432b9e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904103"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Créer, lister ou supprimer une identité attribuée à l’utilisateur à l’aide d’Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Managed Service Identity fournit aux services Azure une identité administrée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, lister et supprimer une identité attribuée à l’utilisateur en utilisant Azure PowerShell.

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md). **Veillez à lire [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Installez [la dernière version d’Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si ce n’est déjà fait.
- Si vous choisissez d’installer et d’utiliser PowerShell localement, vous devez exécuter le module Azure PowerShell version 5.7.0 ou ultérieure pour les besoins de ce tutoriel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-user-assigned-identity"></a>Créer une identité attribuée à l’utilisateur

Pour créer une identité attribuée à l’utilisateur, utilisez la commande [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity). Le paramètre `ResourceGroupName` spécifie le groupe de ressources dans lequel créer l’identité attribuée à l’utilisateur, tandis que le paramètre `-Name` indique son nom. Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lister les identités attribuées à l’utilisateur

Pour lister les identités attribuées à l’utilisateur, utilisez la commande [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity).  Le paramètre `-ResourceGroupName` spécifie le groupe de ressources dans lequel l’identité attribuée à l’utilisateur a été créée.  Remplacez `<RESOURCE GROUP>` par votre propre valeur :

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Dans la réponse, les identités utilisateurs ont une valeur `"Microsoft.ManagedIdentity/userAssignedIdentities"` retournée pour la clé, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Supprimer une identité attribuée à l’utilisateur

Pour supprimer une identité utilisateur, utilisez la commande [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity).  Le paramètre `-ResourceGroupName` spécifie le groupe de ressources où l’identité attribuée à l’utilisateur a été créée, et le paramètre `-Name` spécifie son nom.  Remplacez les valeurs des paramètres `<RESOURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs :

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> La suppression d’une identité attribuée à l’utilisateur ne supprime pas les références des ressources auxquelles elle a été attribuée. Les attributions d’identités doivent être supprimées séparément.

## <a name="related-content"></a>Contenu connexe

Pour obtenir une liste complète et plus d’informations sur les commandes MSI Azure PowerShell, consultez [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
