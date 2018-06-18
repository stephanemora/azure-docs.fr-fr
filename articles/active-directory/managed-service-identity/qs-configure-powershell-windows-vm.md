---
title: Comment configurer la MSI sur une machine virtuelle Azure à l’aide de PowerShell
description: Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide de PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 6981c0f917fb7175f444ceca8c55c0df186774db
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33932317"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurer une identité du service administré (MSI) de machine virtuelle à l’aide de PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité automatiquement gérée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>Dans cet article, vous allez découvrir comment effectuer les opérations Managed Service Identity suivantes sur une machine virtuelle Azure à l’aide de PowerShell :
- 

## <a name="prerequisites"></a>Prérequis


- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md). **Veillez à lire [la différence entre les identités attribuées au système et celles attribuées à l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Installez [la dernière version d’Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si ce n’est déjà fait.

## <a name="system-assigned-identity"></a>identité attribuée au système

Dans cette section, vous allez découvrir comment activer et désactiver l’identité attribuée au système à l’aide d’Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Activer une identité attribuée au système lors de la création d’une machine virtuelle Azure

Pour créer une machine virtuelle Azure avec l’identité attribuée au système activée

1. Consultez l’un des démarrages rapides de machine virtuelle Azure suivants, en ne complétant que les sections nécessaires (« Se connecter à Azure », « Créer un groupe de ressources », « Créer un groupe de mise en réseau », « Créer la machine virtuelle »).
    
    Lorsque vous accédez à la section « Créer la machine virtuelle », apportez une légère modification à la syntaxe de l’applet de commande [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). N’oubliez pas d’ajouter un paramètre `-AssignIdentity "SystemAssigned"` afin de provisionner la machine virtuelle avec l’identité attribuée au système activée, par exemple :
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Créer une machine virtuelle Windows avec PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Créer une machine virtuelle Linux avec PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facultatif) Ajoutez l’extension de machine virtuelle MSI à l’aide du paramètre `-Type` dans l’applet de commande [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Activer une identité attribuée au système sur une machine virtuelle Azure existante

Si vous devez activer une identité attribuée au système sur une machine virtuelle existante :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```powershell
   Login-AzureRmAccount
   ```

2. Commencez par récupérer les propriétés de la machine virtuelle à l’aide de la cmdlet `Get-AzureRmVM`. Ensuite, pour activer une identité attribuée par le système, utilisez le commutateur `-AssignIdentity` sur l’applet de commande [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) :

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Facultatif) Ajoutez l’extension de machine virtuelle MSI à l’aide du paramètre `-Type` dans l’applet de commande [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton. Veillez à indiquer le paramètre `-Location` approprié, correspondant à l’emplacement de la machine virtuelle existante :

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Désactiver l’identité attribuée au système à partir d’une machine virtuelle Azure

> [!NOTE]
>  La désactivation de Managed Service Identity à partir d’une machine virtuelle n’est pas prise en charge actuellement. En attendant, vous pouvez basculer entre des identités attribuées au système et des identités attribuées à l’utilisateur.

Si vous avez une machine virtuelle qui n’a plus besoin de l’identité attribuée au système mais qui a toujours besoin d’identités attribuées à l’utilisateur, utilisez l’applet de commande suivante :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```powershell
   Login-AzureRmAccount
   ```

2. Exécutez l’applet de commande suivante : 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Pour supprimer l’extension de machine virtuelle MSI, utilisez le commutateur -Name avec l’applet de commande [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), en spécifiant le même nom que celui utilisé quand vous avez ajouté l’extension :

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>identité attribuée à l’utilisateur

Dans cette section, vous allez découvrir comment ajouter et supprimer une identité attribuée à l’utilisateur sur une machine virtuelle à l’aide d’Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Attribuer une identité attribuée à l’utilisateur à une machine virtuelle lors de sa création

Pour attribuer une identité attribuée à l’utilisateur à une machine virtuelle Azure lors de la création de la machine virtuelle

1. Consultez l’un des démarrages rapides de machine virtuelle Azure suivants, en ne complétant que les sections nécessaires (« Se connecter à Azure », « Créer un groupe de ressources », « Créer un groupe de mise en réseau », « Créer la machine virtuelle »). 
  
    Quand vous parvenez à la section « Créer la machine virtuelle », apportez une légère modification à la syntaxe de l’applet de commande [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm). Ajoutez les paramètres `-IdentityType UserAssigned` et `-IdentityID ` pour provisionner la machine virtuelle avec une identité attribuée à l’utilisateur.  Remplacez `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` et `<MSI NAME>` par vos propres valeurs.  Par exemple : 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Créer une machine virtuelle Windows avec PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Créer une machine virtuelle Linux avec PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Facultatif) Ajoutez l’extension de machine virtuelle MSI à l’aide du paramètre `-Type` dans l’applet de commande [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton. Veillez à indiquer le paramètre `-Location` approprié, correspondant à l’emplacement de la machine virtuelle existante :
      > [!NOTE]
    > Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Attribuer une identité utilisateur à une machine virtuelle Azure existante

Pour attribuer une identité attribuée à l’utilisateur à une machine virtuelle Azure existante

1. Connectez-vous au portail Azure à l’aide de `Connect-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```powershell
   Connect-AzureRmAccount
   ```

2. Créez une identité attribuée à l’utilisateur à l’aide de l’applet de commande [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity).  Notez la valeur `Id` dans la sortie, car vous en aurez besoin à l’étape suivante.

    > [!IMPORTANT]
    > La création d’identités attribuées à l’utilisateur ne prend en charge que les caractères alphanumériques et le trait d’union (0-9 ou a-z ou A-Z ou -). De plus, le nom doit être limité à 24 caractères pour que l’attribution à la machine virtuelle/au groupe de machines virtuelles identiques fonctionne correctement. Revenez ultérieurement pour des mises à jour. Pour plus d’informations, consultez [FAQ et problèmes connus](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Récupérez les propriétés de la machine virtuelle à l’aide de l’applet de commande `Get-AzureRmVM`. Ensuite, pour attribuer une identité attribuée à l’utilisateur à la machine virtuelle Azure, utilisez les commutateurs `-IdentityType` et `-IdentityID` avec l’applet de commande [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).  La valeur du paramètre `-IdentityId` est la valeur `Id` que vous avez notée à l’étape précédente.  Remplacez `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` et `<USER ASSIGNED IDENTITY NAME>` par vos propres valeurs.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Ajoutez l’extension de machine virtuelle de la MSI à l’aide du paramètre `-Type` sur la cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton. Spécifiez le paramètre `-Location` approprié, correspondant à l’emplacement de la machine virtuelle existante.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Supprimer une identité attribuée à l’utilisateur d’une machine virtuelle Azure

> [!NOTE]
>  La suppression de toutes les identités attribuées à l’utilisateur d’une machine virtuelle n’est actuellement pas prise en charge, sauf si vous avez une identité attribuée au système. Revenez ultérieurement pour des mises à jour.

Si votre machine virtuelle a plusieurs identités attribuées à l’utilisateur, vous pouvez les supprimer toutes sauf la dernière à l’aide des commandes suivantes. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. `<MSI NAME>` est la propriété de nom de l’identité attribuée à l’utilisateur, qui doit rester sur la machine virtuelle. Ces informations sont accessibles dans la section d’identité de la machine virtuelle à l’aide de la commande `az vm show` :

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Si votre machine virtuelle dispose à la fois d’identités attribuées à l’utilisateur et d’identités attribuées au système, vous pouvez supprimer toutes les identités attribuées à l’utilisateur en choisissant de n’utiliser que des identités attribuées au système. Utilisez la commande suivante :

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenu connexe

- [Vue d’ensemble de l’identité du service administré](overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez :
  
  - [Créer une machine virtuelle Windows avec PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Créer une machine virtuelle Linux avec PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
