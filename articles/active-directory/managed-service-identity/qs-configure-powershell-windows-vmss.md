---
title: Comment configurer MSI sur un groupe de machines virtuelles identiques Azure à l’aide de PowerShell
description: Cet article contient des instructions pas à pas pour la configuration d’identités attribuées par le système et un utilisateur sur un groupe de machines virtuelles identiques Azure à l’aide de PowerShell.
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
ms.openlocfilehash: 97c5e2dde3faeaad13317597bef4f70455d22102
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930127"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Configurer une MSI de groupe de machines virtuelles identiques à l’aide de PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment effectuer les opérations MSI suivantes sur un groupe de machines virtuelles identiques Azure à l’aide de PowerShell :
- Activer et désactiver l’identité attribuée par le système sur un groupe de machines virtuelles identiques Azure
- Ajouter et supprimer une identité attribuée par l’utilisateur sur un groupe de machines virtuelles identiques Azure

## <a name="prerequisites"></a>Prérequis


- Si vous ne connaissez pas MSI, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identités attribuées par le système et celles attribuées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Installez [la dernière version d’Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si ce n’est déjà fait. 

## <a name="system-assigned-managed-identity"></a>Identité gérée attribuée par le système

Dans cette section, vous allez découvrir comment activer et supprimer une identité attribuée par l’utilisateur à l’aide d’Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Activer une identité attribuée par le système lors de la création d’un groupe de machines virtuelles identiques Azure

Pour créer un groupe de machines virtuelles identiques avec l’identité attribuée par le système activée :

1. Pour créer un groupe de machines virtuelles identiques avec une identité attribuée par le système, voir l’*Exemple 1* dans l’article de référence sur la cmdlet [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig).  Ajoutez le paramètre `-IdentityType SystemAssigned` à la cmdlet `New-AzureRmVmssConfig` :

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Facultatif) Ajoutez l’extension de groupe de machines virtuelles identiques MSI en utilisant les paramètres `-Name` et `-Type` sur la cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

    > [!NOTE]
    > Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Activer une identité attribuée par le système sur un groupe de machines virtuelles identiques Azure existant

Si vous devez activer une identité attribuée par le système sur un groupe de machines virtuelles identiques Azure existant :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```powershell
   Login-AzureRmAccount
   ```

2. Commencez par récupérer les propriétés du groupe de machines virtuelles identiques à l’aide de la cmdlet [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Ensuite, pour activer une identité attribuée par le système, utilisez le commutateur `-IdentityType` sur l’applet de commande [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) :

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Ajoutez l’extension de groupe de machines virtuelles identiques MSI en utilisant les paramètres `-Name` et `-Type` sur la cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Désactiver l’identité attribuée par le système d’un groupe de machines virtuelles identiques Azure

> [!NOTE]
> La désactivation de MSI d’un groupe de machines virtuelles identiques n’est pas prise en charge actuellement. En attendant, vous pouvez basculer entre des identités attribuées par le système et des identités attribuées par l’utilisateur.

Si vous avez un groupe de machines virtuelles identiques qui n’a plus besoin d’identité attribuée par le système mais qui a toujours besoin d’identités attribuées par l’utilisateur, utilisez la cmdlet suivante :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

2. Exécutez l’applet de commande suivante :

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Identité attribuée par l’utilisateur

Dans cette section, vous allez découvrir comment ajouter et supprimer une identité attribuée par l’utilisateur sur un groupe de machines virtuelles identiques à l’aide d’Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Attribuer une identité attribuée par l’utilisateur lors de la création d’un groupe de machines virtuelles identiques Azure

La création d’un groupe de machines virtuelles identiques avec une identité attribuée par l’utilisateur via PowerShell n’est pas actuellement prise en charge. Lisez la section suivante pour savoir comment ajouter une identité attribuée par l’utilisateur à un groupe de machines virtuelles identiques existant. Revenez ultérieurement pour des mises à jour.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Attribuer une identité attribuée par l’utilisateur à un groupe de machines virtuelles identiques Azure existant

Pour attribuer une identité attribuée par l’utilisateur à un groupe de machines virtuelles identiques Azure existant :

1. Connectez-vous au portail Azure à l’aide de `Connect-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```powershell
   Connect-AzureRmAccount
   ```

2. Commencez par récupérer les propriétés de la machine virtuelle à l’aide de la cmdlet `Get-AzureRmVM`. Ensuite, pour attribuer une identité attribuée par l’utilisateur au groupe de machines virtuelles identiques Azure, utilisez les commutateurs `-IdentityType` et `-IdentityID` avec la cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm). Remplacez `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>` et `USER ASSIGNED ID2` par vos propres valeurs.

   > [!IMPORTANT]
   > La création d’identités attribuées par l’utilisateur ne prend en charge que les caractères alphanumériques et traits d’union (0-9 ou a-z ou A-Z ou -). En outre, le nom doit être limité à 24 caractères pour que l’attribution à la machine virtuelle ou au groupe de machines virtuelles identiques fonctionne correctement. Revenez ultérieurement pour des mises à jour. Pour plus d’informations, voir [FAQ et problèmes connus](known-issues.md).


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Supprimer une identité attribuée par l’utilisateur d’un groupe de machines virtuelles identiques Azure

> [!NOTE]
> La suppression de toutes les identités attribuées par l’utilisateur d’un groupe de machines virtuelles identiques n’est actuellement pas prise en charge, sauf si vous avez une identité attribuée par le système. Revenez ultérieurement pour des mises à jour.

Si votre groupe de machines virtuelles identiques a plusieurs identités attribuées par l’utilisateur, vous pouvez les supprimer toutes sauf la dernière à l’aide des commandes suivantes. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VMSS NAME>` par vos propres valeurs. `<MSI NAME>` est la propriété de nom de l’identité attribuée par l’utilisateur, qui doit rester sur le groupe de machines virtuelles identiques. Ces informations sont accessibles dans la section identity du groupe de machines virtuelles identiques à l’aide de la commande `az vmss show` :

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Si votre groupe de machines virtuelles identiques dispose à la fois d’identités attribuées par l’utilisateur et d’identités attribuées par le système, vous pouvez supprimer toutes les identités attribuées par l’utilisateur en choisissant de n’utiliser que des identités attribuées par le système. Utilisez la commande suivante :

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Contenu connexe

- [Vue d’ensemble de l’identité du service administré](overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez :
  
  - [Créer une machine virtuelle Windows avec PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Créer une machine virtuelle Linux avec PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















