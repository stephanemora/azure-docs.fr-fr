---
title: Guide de configuration d’identités managées pour ressources Azure sur un groupe de machines virtuelles identiques en utilisant PowerShell
description: Instructions détaillées pour la configuration d’identités managées affectées par le système et un utilisateur sur un groupe de machines virtuelles identiques en utilisant PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 516216e435722e27e7c3c9330db4991e280c4014
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54429058"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Configurer des identités managées pour ressources Azure sur un groupe de machines virtuelles identiques en utilisant PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Les identités managées pour ressources Azure fournissent des services Azure avec une identité managée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Cet article explique comment effectuer les opérations d’identités managées pour ressources Azure sur un groupe de machines virtuelles identiques en utilisant PowerShell :
- Activer et désactiver l’identité managée affectée par le système sur un groupe de machines virtuelles identiques
- Ajouter et supprimer une identité managée affectée par l’utilisateur sur un groupe de machines virtuelles identiques

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour ressources Azure, consultez la [section Vue d’ensemble](overview.md). **Veillez à lire la [différence entre les identités managées affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer de ces affectations de contrôle d'accès basé sur les rôles Azure :

    > [!NOTE]
    > Aucune attribution de rôle d'annuaire Azure AD supplémentaire n’est requise.

    - [Contributeur de machines virtuelles](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) pour créer un groupe de machines virtuelles identiques, puis activer et supprimer une identité managée affectée par le système et/ou par l’utilisateur dans un groupe de machines virtuelles identiques.
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) pour créer une identité managée affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour attribuer et supprimer une identité managée affectée par l’utilisateur dans un groupe de machines virtuelles identiques.
- Installez [la dernière version d’Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si ce n’est déjà fait. 

## <a name="system-assigned-managed-identity"></a>Identité managée affectée par le système

Dans cette section, vous allez découvrir comment activer et supprimer une identité managée affectée par le système en utilisant Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Activer une identité managée affectée par le système lors de la création d’un groupe de machines virtuelles identiques Azure

Pour créer un groupe de machines virtuelles identiques avec l’identité managée affectée par le système activée :

1. Pour créer un groupe de machines virtuelles identiques avec une identité managée affectée par le système, voir l’*Exemple 1* dans l’article de référence sur l’applet de commande [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig).  Ajoutez le paramètre `-IdentityType SystemAssigned` à la cmdlet `New-AzureRmVmssConfig` :

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Facultatif) Ajoutez l’extension de groupe de machines virtuelles identiques d’identités managées pour ressources Azure en utilisant les paramètres `-Name` et `-Type` avec l’applet de commande [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de groupe de machines virtuelles identiques, et le nommer au moyen du paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

    > [!NOTE]
    > Cette étape est facultative, car vous pouvez également utiliser le point de terminaison d’identité IMDS (Instance Metadata Service) Azure pour récupérer des jetons.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Activer une identité managée affectée par le système sur un groupe de machines virtuelles identiques Azure existant

Si vous devez activer une identité managée affectée par le système sur un groupe de machines virtuelles identiques Azure existant :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur le groupe de machines virtuelles identiques, comme « Contributeur de machines virtuelles » :

   ```powershell
   Login-AzureRmAccount
   ```

2. Tout d’abord, récupérez les propriétés du groupe de machines virtuelles identiques au moyen de l’applet de commande [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Ensuite, pour activer une identité managée affectée par le système, utilisez le commutateur `-IdentityType` avec l’applet de commande [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) :

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Ajoutez l’extension de groupe de machines virtuelles identiques d’identités managées pour ressources Azure en utilisant les paramètres `-Name` et `-Type` avec l’applet de commande [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de groupe de machines virtuelles identiques, et le nommer au moyen du paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Désactiver l’identité managée affectée par le système d’un groupe de machines virtuelles identiques Azure

Si vous disposez d’un groupe de machines virtuelles identiques qui n’a plus besoin de l’identité managée affectée par le système, mais qui a toujours besoin d’identités managées affectées par l’utilisateur, utilisez l’applet de commande suivante :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur le groupe de machines virtuelles identiques, comme « Contributeur de machines virtuelles » :

2. Exécutez l’applet de commande suivante :

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Si vous disposez d’un groupe de machines virtuelles identiques qui n’a plus besoin de l’identité managée affectée par le système et qui n’a pas d’identité managée affectée par l’utilisateur, utilisez les commandes suivantes :

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identité managée affectée par l’utilisateur

Dans cette section, vous allez découvrir comment ajouter et supprimer une identité managée affectée par l’utilisateur dans un groupe de machines virtuelles identiques en utilisant Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Attribuer une identité managée affectée par l’utilisateur lors de la création d’un groupe de machines virtuelles identiques Azure

La création d’un groupe de machines virtuelles identiques avec une identité managée affectée par l’utilisateur via PowerShell n’est pas prise en charge actuellement. Pour savoir comment ajouter une identité managée affectée par l’utilisateur à un groupe de machines virtuelles identiques existant, lisez la section suivante. Revenez ultérieurement pour des mises à jour.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Attribuer une identité managée affectée par l’utilisateur à un groupe de machines virtuelles identiques Azure existant

Pour attribuer une identité managée affectée par l’utilisateur à un groupe de machines virtuelles identiques Azure existant :

1. Connectez-vous au portail Azure à l’aide de `Connect-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur le groupe de machines virtuelles identiques, comme « Contributeur de machines virtuelles » :

   ```powershell
   Connect-AzureRmAccount
   ```

2. Tout d’abord, récupérez les propriétés du groupe de machines virtuelles identiques au moyen de l’applet de commande `Get-AzureRmVM`. Ensuite, pour attribue une identité managée affectée par l’utilisateur au groupe de machines virtuelles identiques Azure, utilisez les commutateurs `-IdentityType` et `-IdentityID` avec l’applet de commande [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Remplacez `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>` et `USER ASSIGNED ID2` par vos propres valeurs.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Supprimer d’un groupe de machines virtuelles identiques Azure une identité managée affectée par l’utilisateur

Si votre groupe de machines virtuelles identiques dispose de plusieurs identités managées affectées par l’utilisateur, vous pouvez toutes les supprimer, sauf la dernière, au moyen des commandes suivantes. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VMSS NAME>` par vos propres valeurs. `<USER ASSIGNED IDENTITY NAME>` est la propriété de nom de l’identité managée affectée par l’utilisateur, qui doit rester sur le groupe de machines virtuelles identiques. Ces informations sont accessibles dans la section d’identité du groupe de machines virtuelles identiques à l’aide de `az vmss show` :

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Si votre groupe de machines virtuelles identiques ne dispose pas d’une identité managée affectée par le système, et souhaitez supprimer toutes les identités managées affectées par l’utilisateur qu’elle contient, utilisez la commande suivante :

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Si votre groupe de machines virtuelles identiques dispose à la fois d’identités managées affectées par l’utilisateur et d’identités affectées par le système, vous pouvez supprimer toutes les identités managées affectées par l’utilisateur en choisissant de n’utiliser que l’identité managée affectée par le système.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des identités managées pour les ressources Azure](overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez :
  
  - [Créer une machine virtuelle Windows avec PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Créer une machine virtuelle Linux avec PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















