---
title: Réinitialiser les Services Bureau à distance ou le mot de passe administrateur associé dans une machine virtuelle Windows | Microsoft Docs
description: Découvrez comment réinitialiser un mot de passe de compte ou des services Bureau à distance sur une machine virtuelle Windows à l’aide du Portail Azure ou d’Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f825d3419a8aebeae4500a383740058e984a2f2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420827"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Réinitialiser les Services Bureau à distance ou le mot de passe administrateur associé dans une machine virtuelle Windows
Si vous ne pouvez pas vous connecter à une machine virtuelle Windows, vous pouvez réinitialiser le mot de passe d’administrateur local ou la configuration du service Bureau à distance (pas de prise en charge sur les contrôleurs de domaine Windows). Vous pouvez utiliser le portail Azure ou l’extension d’accès aux machines virtuelles dans Azure PowerShell pour réinitialiser le mot de passe. Une fois que vous êtes connecté à la machine virtuelle, réinitialisez le mot de passe pour cet administrateur local.  
Si vous utilisez PowerShell, assurez-vous d’avoir [installé et configuré le dernier module PowerShell](/powershell/azure/overview) et d’être connecté à votre abonnement Azure. Vous pouvez également [effectuer ces étapes pour les machines virtuelles créées à l’aide du modèle de déploiement classique](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

Vous pouvez réinitialiser les informations d’identification et les Services Bureau à distance de plusieurs manières :

- [Réinitialisation en utilisant le portail Azure](#reset-by-using-the-azure-portal)
- [Réinitialisation en utilisant l’extension VMAccess et PowerShell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Réinitialisation en utilisant le portail Azure
Connectez-vous au Portail Azure, puis, dans le menu de gauche, sélectionnez **Machines virtuelles**.

### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe de compte d’administrateur local**

Sélectionnez votre machine virtuelle Windows, puis **Réinitialiser le mot de passe** sous **Support + dépannage**. La fenêtre **Réinitialiser le mot de passe** s’affiche.

Sélectionnez **Réinitialiser le mot de passe**, entrez un nom d’utilisateur et un mot de passe, puis sélectionnez **Mettre à jour**. Essayez à nouveau de vous connecter à votre machine virtuelle.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Réinitialiser la configuration des Services Bureau à distance**

Sélectionnez votre machine virtuelle Windows, puis **Réinitialiser le mot de passe** sous **Support + dépannage**. La fenêtre **Réinitialiser le mot de passe** s’affiche. 

Sélectionnez **Réinitialiser la configuration uniquement** , puis sélectionnez **Mettre à jour**. Essayez à nouveau de vous connecter à votre machine virtuelle.


## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Réinitialisation en utilisant l’extension VMAccess et PowerShell
Assurez-vous d’avoir [installé et configuré le dernier module PowerShell ](/powershell/azure/overview)et d’être connecté à votre abonnement Azure à l’aide de l’applet de commande [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount).

### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe de compte d’administrateur local**
Réinitialisez le mot de passe ou le nom d’utilisateur de l’administrateur à l’aide de l’applet de commande PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). La version du paramètre `typeHandlerVersion` doit être 2.0 ou ultérieure, car la version 1 est déconseillée. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> Si vous saisissez un nom différent de celui du compte d’administrateur local actuel sur votre machine virtuelle, l’extension VMAccess ajoute un compte d’administrateur local avec ce nom et affecte le mot de passe spécifié à ce compte. Si le compte d’administrateur local sur votre machine virtuelle est désactivé, l’extension VMAccess réinitialise le mot de passe. Si le compte est désactivé, l’extension VMAccess l’active.

### <a name="reset-the-remote-desktop-services-configuration"></a>**Réinitialiser la configuration des Services Bureau à distance**
Réinitialisez l’accès à distance à votre machine virtuelle avec l’applet de commande PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). L’exemple suivant réinitialise l’extension d’accès nommée `myVMAccess`, sur la machine virtuelle nommée `myVM`, dans le groupe de ressources nommé `myResourceGroup` :

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> En toutes circonstances, une machine virtuelle ne peut avoir qu’un seul agent d’accès de machine virtuelle. Pour définir les propriétés de l’agent d’accès aux machines virtuelles, utilisez l’option `-ForceRerun`. Lorsque vous utilisez `-ForceRerun`, vérifiez que vous utilisez le même nom pour l’agent d’accès à la machine virtuelle que vous avez peut-être utilisé dans une commande précédente.

Si vous ne pouvez toujours pas vous connecter à distance à votre machine virtuelle, consultez [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure exécutant Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Si vous perdez la connexion au contrôleur de domaine Windows, vous devez le restaurer à partir d’une sauvegarde de contrôleur de domaine.

## <a name="next-steps"></a>Étapes suivantes
Si l’extension d’accès aux machines virtuelles Azure ne répond pas et que vous ne pouvez pas réinitialiser le mot de passe, vous pouvez [réinitialiser le mot de passe Windows local en mode hors connexion](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette méthode est plus avancée et nécessite que vous connectiez le disque dur virtuel de la machine virtuelle problématique à une autre machine virtuelle. Suivez d’abord la procédure décrite dans cet article et n’utilisez la méthode de réinitialisation du mot de passe en mode hors connexion qu’en cas d’échec de cette procédure.

[Découvrez les extensions et fonctionnalités des machines virtuelles Azure](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

